# Decoupling in Angular: Letting the Code Tell You What It Needs

## Full Talk Script (v8 - Teaching Version)

---

### Slide 1 – About Me

**On screen:**

```
Dor Peled
@Knat-Dev

Software Engineer @ Coralogix
I tend to stay with hard things longer than is comfortable
```

**You say:**

"Hey everyone, I'm Dor. I'm a software engineer at Coralogix.

I've noticed something about myself over the years.

When something gets complicated or messy, I usually don't bounce right away. I tend to sit with it and try to understand what's actually going on.

That shows up in different places. In music, I play technical metal, where shortcuts are very obvious. In reading, I gravitate toward long, dense series like Malazan Book of the Fallen and The Wheel of Time.

Not because they're impressive, but because they don't explain themselves quickly. You have to hold a lot in your head, trust that structure exists, and keep going even when it's unclear.

And large Angular codebases behave the same way. They don't fail immediately. They accumulate complexity quietly. And if you don't stay with them long enough, you miss the signals that tell you what they actually need.

So this talk isn't about best practices or patterns. It's about what I learned by staying with components that were under pressure.

How do you know when a component needs to be split up? And more importantly, how do you split it without guessing?

I'm gonna give you a mental model. Four tools, and the cues that tell you when to use each one."

---

### Slide 2 – The Universal Story

**On screen:**

```
Every Angular dev has written this component:

Month 1: Clean, simple, works great
Month 2: "Why is this so hard to change?"
```

**You say:**

"So here's a story you've probably lived.

Month one, you build a component. It's clean. Two inputs, maybe an output. Life is good.

Month two, product wants a variation. And you realize... this thing is stuck. Every change breaks something else."

---

### Slide 3 – Our Example: DataGrid

**On screen:**

```typescript
// Month 1
@Component({ selector: 'app-data-grid' })
export class DataGridComponent<T> {
  data = input<T[]>([]);
  columns = input<ColumnDef<T>[]>([]);
  loading = input(false);
}
```

**You say:**

"Let's use a data grid as our example. Everyone's built one of these, right?

Month one - three inputs. Data, columns, loading state. That's it. Clean."

---

### Slide 4 – Month 2

**On screen:**

```typescript
// Month 2
@Component({ selector: 'app-list' })
export class ListComponent {
  items = input<Item[]>([]);
  loading = input(false);
  sortable = input(false);
  showHeader = input(false);
  persistState = input(false);

  storageKey = input<string>();
  initialState = input<ListState>();
  stateSaved = output<void>();

  #api = inject(ApiService);
  #storage = inject(StorageService);

  ngOnInit() {
    if (this.persistState()) {
      // 30 lines of restoration logic...
    }
  }
}
```

**You say:**

"Month two. Product shows up. 'Can we add sorting?' Sure. 'A header option?' Okay. 'Remember state?' Fine.

And this is month TWO. Imagine month six. Imagine year two.

Now look at this code. Really look.

Boolean flags - sortable, showHeader, persistState. Dependent inputs - storageKey, initialState. An output. Two injected services. Lifecycle code.

We didn't inherit this mess. We built it. One reasonable feature request at a time.

This is where we're starting today. This is what we're going to systematically take apart."

---

<p align="center">
  <img src="./assets/this-is-fine.jpg" alt="This is fine meme" width="500" />
</p>

---

### Slide 5 – The Real Problem

**On screen:**

```
Coupling isn't the problem.

Hidden coupling is.
```

**You say:**

"But here's what I want you to understand.

Coupling isn't bad. Every system has coupling. The problem is when coupling is hidden. When you can't see it, you can't manage it.

Today I'll show you four tools Angular gives us, and the tells that show you when to reach for each one."

---

### Slide 6 – The Four Tools

**On screen:**

```
1. Inputs/Outputs     → Parent configures child
2. Content Projection → Structure varies
3. Strategy via DI    → Behavior A or B
4. Directives         → Behavior A and B and C
```

**You say:**

"These are our four tools. Each one solves a different problem.

The trick isn't knowing the tools - you probably know all of these. The trick is recognizing WHEN to use each one."

---

### Slide 7 – Tool 1: Inputs/Outputs

**On screen:**

```
The Baseline

Parent decides WHAT.
Child decides HOW.
```

**You say:**

"Inputs and outputs. Everyone's first tool.

Parent passes data down, child emits events up. Simple. This is where you start, and honestly, it's where you should stay as long as possible."

---

### Slide 8 – Inputs: The Code

**On screen:**

```typescript
@Component({ selector: 'app-data-grid' })
export class DataGridComponent<T> {
  data = input<T[]>([]);
  columns = input<ColumnDef<T>[]>([]);
  loading = input(false);

  rowClicked = output<T>();
}
```

```html
<app-data-grid
  [data]="items"
  [columns]="columns"
  [loading]="isLoading"
  (rowClicked)="onSelect($event)"
/>
```

**You say:**

"Clean contract. Parent owns the data, child owns the rendering. They can evolve independently.

This is the right abstraction for simple cases."

---

### Slide 9 – The Signal

**On screen:**

```typescript
sortable = input(false);
showHeader = input(false);
persistState = input(false);
```

**You say:**

"Look at these. Boolean flags. sortable, showHeader, persistState.

Each one is a feature toggle. The component is asking: 'What features am I running today?'

That's the signal. When you see boolean flags accumulating, the component is trying to be too many things.

But here's the thing - and we'll come back to this - boolean flags are just the surface. Look back at the Month 2 code. Notice how storageKey and initialState only matter when persistState is true? They travel together. The truth is deeper than just flags.

For now, let's start with the simplest case. One of these flags isn't like the others."

---

### Slide 10 – The Structural Flag

**On screen:**

```typescript
// From our Month 2 component:
showHeader = input(false);
```

```html
@if (showHeader()) {
<app-header />
}
<div class="list-body">...</div>
```

**You say:**

"Let's start with showHeader. This one is different from sortable or persistState.

It doesn't change behavior - it controls structure. What DOM elements exist.

Look at this. showHeader. And somewhere in the template, an @if that renders a header.

Why does the LIST decide whether a header exists? That's not data. That's not behavior. That's the parent's concern.

This is the simplest extraction we can make."

---

### Slide 11 – Extract the @if

**On screen:**

```html
<!-- Before: component decides structure -->
<app-list [showHeader]="true" />

<!-- After: parent decides structure -->
<app-list>
  <app-header />
</app-list>
```

**You say:**

"Watch what happens when you extract the conditional.

Before: showHeader equals true. Component renders the header internally.

After: No boolean. Parent just... puts the header there. Or doesn't.

The @if block becomes an ng-content slot. The boolean vanishes.

Content projection isn't a 'layout feature'. It's what happens when you extract structural decisions from components."

---

### Slide 12 – Tool 2: Content Projection

**On screen:**

```
When STRUCTURE varies.

Not behavior. Structure.
```

**You say:**

"Content projection. The parent passes structure in, not configuration down.

Use this when the variation is about WHAT goes WHERE. Headers, footers, custom templates."

---

### Slide 13 – Content Projection: The Code

**On screen:**

```typescript
@Component({
  selector: 'app-card',
  template: `
    <div class="card">
      <div class="header">
        <ng-content select="[header]" />
      </div>
      <ng-content />
    </div>
  `,
})
export class CardComponent {}
```

```html
<app-card>
  <h2 header>My Title</h2>
  <p>Card content here</p>
</app-card>
```

**You say:**

"The card owns layout. The consumer owns content.

Different consumers can put different things in, but the card doesn't need to know or care."

---

### Slide 14 – The Limit

**On screen:**

```
Doesn't work for:
  - "Save to localStorage vs server"
  - "Persist state when flag is true"

You need different behavior, not structure.
```

**You say:**

"So we handled showHeader. That boolean is gone. One down.

But look back at Month 2. What about persistState? And its friends - storageKey, initialState, the services?

Can we ng-content an HttpClient? Can we put localStorage in a slot?

No. Content projection is for DOM nodes. Visual stuff.

When the variation is about WHAT HAPPENS, not WHAT APPEARS - that's a different tool."

---

### Slide 15 – Tool 3: Strategy via DI

**On screen:**

```
When behavior is exclusive.

A or B. Never both.
```

**You say:**

"Strategy pattern via dependency injection.

This is for mutually exclusive behavior. Exactly one implementation wins at runtime."

---

### Slide 16 – The Behavioral Bundle

**On screen:**

```typescript
persistState = input(false);

storageKey = input<string>();
initialState = input<ListState>();
stateSaved = output<void>();

#http = inject(HttpClient);
#storage = inject(LocalStorageService);

ngOnInit() {
  if (this.persistState()) {
    // 30 lines of restoration logic...
  }
}
```

**You say:**

"Remember earlier I said the truth is deeper than just boolean flags?

This is it. Look at our Month 2 component again - just the persistence part.

One flag - persistState. But it's not alone.

storageKey - WHERE to save. initialState - what to restore. stateSaved - notify when done.

Then the services. HttpClient for server. LocalStorageService for browser. And the lifecycle code.

All of this only matters if persistState is true. These things travel together. They're a behavioral unit.

And here's the key insight: you can't content-project behavior. You can't put an HttpClient in an ng-content slot.

So what do we do when a whole bundle of behavior needs to move?"

---

### Slide 17 – Strategy: The Problem

**On screen:**

```
That whole bundle needs to move. But where?

| Context | Storage      |
| Admin   | Server       |
| Public  | localStorage |
| Preview | Don't save   |
```

**You say:**

"That whole bundle - the flag, the inputs, the services, the lifecycle code - needs to move OUT of the component.

But WHERE it goes depends on context.

Admin dashboard? Save to server. Public view? localStorage. Preview mode? Don't save at all.

Same component, different storage behavior.

Old way: another boolean flag, another if-statement. New way: the component doesn't know. Doesn't care."

---

### Slide 18 – Strategy: The Interface

**On screen:**

```typescript
// Define WHAT, not HOW
export interface StorageStrategy {
  save(key: string, data: unknown): void;
  load(key: string): unknown | null;
}

// Create a token
export const STORAGE_STRATEGY =
  new InjectionToken<StorageStrategy>('StorageStrategy');
```

**You say:**

"Step one - define the contract. Save and load. That's it.

The token lets Angular swap implementations."

---

### Slide 19 – Strategy: The Implementations

**On screen:**

```typescript
// Implementation A: localStorage
export class LocalStorageStrategy
  implements StorageStrategy
{
  save(key: string, data: unknown) {
    localStorage.setItem(key, JSON.stringify(data));
  }
  load(key: string) {
    return JSON.parse(localStorage.getItem(key) ?? 'null');
  }
}

// Implementation B: Server
export class ServerStorageStrategy
  implements StorageStrategy
{
  save(key: string, data: unknown) {
    this.http.post('/api/preferences', { key, data });
  }
  load(key: string) {
    return this.http.get(`/api/preferences/${key}`);
  }
}
```

**You say:**

"Two implementations. Same interface. The grid doesn't know which one it's using."

---

### Slide 20 – Strategy: The Provider

**On screen:**

```typescript
// Context A: Admin dashboard
@Component({
  providers: [
    {
      provide: STORAGE_STRATEGY,
      useClass: ServerStorageStrategy,
    },
  ],
})
export class AdminDashboard {}

// Context B: Public view
@Component({
  providers: [
    {
      provide: STORAGE_STRATEGY,
      useClass: LocalStorageStrategy,
    },
  ],
})
export class PublicView {}
```

**You say:**

"The decision lives in the provider. Same grid component, different storage behavior.

Zero if-statements in the grid. The context decides, not the component."

---

### Slide 21 – Provider Functions

**On screen:**

```typescript
// Bundle defaults into a reusable function
export function provideStorageDefaults(): Provider[] {
  return [
    {
      provide: STORAGE_STRATEGY,
      useClass: LocalStorageStrategy,
    },
  ];
}

// Provide once at app or feature level
bootstrapApplication(AppComponent, {
  providers: [provideStorageDefaults()],
});

// Override only where needed
@Component({
  providers: [
    {
      provide: STORAGE_STRATEGY,
      useClass: ServerStorageStrategy,
    },
  ],
})
export class AdminDashboard {}
```

**You say:**

"Pro tip - bundle your defaults into a provider function.

Call it once at the top. Everything below gets the default. Override only where you need something different.

DI hierarchy does the rest. Closest parent wins."

---

### Slide 22 – The Cue: God Component

**On screen:**

```typescript
// The grid owns ALL the behaviors:
@Component(...)
export class DataGridComponent {
  #storage = inject(STORAGE_STRATEGY);
  #sorter = inject(SORT_STRATEGY);
  #filter = inject(FILTER_STRATEGY);
  #selection = inject(SELECTION_STRATEGY);
  // Can't opt out of any of these
}
```

**You say:**

"But here's the cue that something's wrong.

The component is injecting four strategies. Six. Ten. It's becoming a god component.

Even with provider functions, you can't opt out. Every grid has storage, sorting, filtering, selection - whether you want them or not.

The component is doing too much. It owns behaviors that should be optional."

---

<p align="center">
  <img src="./assets/one-does-not-simply.jpg" alt="One does not simply inject 6 strategies" width="500" />
</p>

---

### Slide 23 – Tool 4: Directive Composition

**On screen:**

```
What's LEFT after the right extractions.

The smallest unit of reusable behavior.
```

**You say:**

"Look back at Month 6. We handled showHeader - that's content projection now. We handled persistState and its bundle - that's a strategy now.

What's left? sortable.

It doesn't control structure. It's not a bundle of dependent inputs and services. It's just... a behavior. Either the list is sortable, or it's not.

No branching. No context questions. Present or absent.

That's a directive. The smallest unit of reusable behavior that survived extraction.

Directives aren't 'yet another tool.' They're what's LEFT when you've done the other extractions right."

---

### Slide 24 – Directives: The Code

**On screen:**

```typescript
@Directive({ selector: 'app-data-grid[persistColumns]' })
export class PersistColumnsDirective {
  #storage = inject(STORAGE_STRATEGY);
  // Uses whatever strategy is provided above in the tree
}

@Directive({ selector: 'app-data-grid[sortable]' })
export class SortableDirective {
  #grid = inject(DataGridComponent);
  // Adds sorting behavior
}
```

**You say:**

"Each directive owns one behavior.

The directive can use a strategy internally. It picks up whatever's provided in the DI tree - from the provider function we set up earlier.

The directive is composable - you add it or you don't. The strategy inside is exclusive - localStorage OR server, never both."

---

### Slide 25 – Directives: Override When Needed

**On screen:**

```typescript
// Most consumers: just add the directive, default works
<app-data-grid persistColumns [data]="items" />

// Admin dashboard needs server storage:
@Component({
  providers: [
    { provide: STORAGE_STRATEGY, useClass: ServerStorageStrategy }
  ]
})
export class AdminDashboard {}

// Template is identical - directive picks up the override
<app-data-grid persistColumns [data]="items" />
```

**You say:**

"Most consumers just add the directive. The default works.

But when you need different behavior, override the token in your component. The directive picks it up through DI hierarchy. Zero changes to the template."

---

### Slide 26 – Directives: The Usage

**On screen:**

```html
<!-- Simple grid: just sorting -->
<app-data-grid sortable [data]="items" />

<!-- Full-featured: everything -->
<app-data-grid
  sortable
  filterable
  persistColumns
  contextMenu
  [data]="items"
/>
```

**You say:**

"Compose what you need. Skip what you don't.

Simple view gets one directive. Power user view gets four. Same component."

---

### Slide 27 – Directive Communication

**On screen:**

```typescript
@Directive({ selector: 'app-data-grid[sortable]' })
export class SortableDirective {
  // Inject the host component
  #grid = inject(DataGridComponent);

  constructor() {
    // React to grid state via signals
    effect(() => {
      const data = this.#grid.data();
      // Apply sorting...
    });
  }
}
```

**You say:**

"How do directives talk to the component? They inject it.

The component exposes signals. Directives read them, react to them, modify behavior.

No special events. No services between them. Just signals."

---

### Slide 28 – The Sign: Repeated Patterns

**On screen:**

```html
<!-- Page A -->
<app-data-grid
  sortable
  filterable
  persistColumns
  contextMenu
/>

<!-- Page B -->
<app-data-grid
  sortable
  filterable
  persistColumns
  contextMenu
/>

<!-- Page C -->
<app-data-grid
  sortable
  filterable
  persistColumns
  contextMenu
/>
```

**You say:**

"But here's the sign that directives need one more step.

Same four directives. Copy-pasted three times. That's implicit coupling through repetition.

If this pattern appears three times, it's not a coincidence. It's a concept that needs a name."

---

<p align="center">
  <img src="./assets/distracted-boyfriend.jpg" alt="Distracted boyfriend meme" width="500" />
</p>

---

### Slide 29 – The Promotion Rule

**On screen:**

```
Two times is coincidence.
Three times is a concept.

Name it.
```

**You say:**

"This is the core insight.

Two times might be coincidence. Three times? That's a concept hiding in plain sight.

When you see the same pattern three times, the code is telling you something's missing from your vocabulary."

---

### Slide 30 – hostDirectives

**On screen:**

```typescript
@Directive({
  selector: '[simpleGrid]',
  hostDirectives: [SortableDirective, FilterableDirective],
})
export class SimpleGridDirective {}

@Directive({
  selector: '[powerGrid]',
  hostDirectives: [
    SortableDirective,
    FilterableDirective,
    PersistColumnsDirective,
    ContextMenuDirective,
  ],
})
export class PowerGridDirective {}
```

**You say:**

"Host directives let you bundle directives into a named concept.

Simple grid, power grid. One attribute instead of four. And now you can talk about it, test it, document it."

---

### Slide 31 – Before/After

**On screen:**

```html
<!-- Before: 4 attributes everywhere -->
<app-data-grid
  sortable
  filterable
  persistColumns
  contextMenu
  [data]="items"
/>

<!-- After: 1 named concept -->
<app-data-grid powerGrid [data]="items" />
```

**You say:**

"Four attributes become one.

But more importantly - the pattern has a name now. 'Power grid' is a thing in your codebase. It's not just 'grid with these four directives'."

---

<p align="center">
  <img src="./assets/pam-theyre-different.jpg" alt="Pam from The Office meme" width="400" />
</p>

---

### Slide 32 – Coordinator Directive

**On screen:**

```typescript
// Sorting and persistence need to work together:
// When sort changes → persist the new sort
// On init → restore persisted sort

@Directive({
  selector: 'app-data-grid[persistedSort]',
  hostDirectives: [
    SortableDirective,
    PersistColumnsDirective,
  ],
})
export class PersistedSortDirective {
  #sortable = inject(SortableDirective);
  #persist = inject(PersistColumnsDirective);

  constructor() {
    // Coordinate the coupling
    effect(() => {
      const sort = this.#sortable.currentSort();
      this.#persist.save('sort', sort);
    });
  }
}
```

**You say:**

"Here's a powerful pattern. Sometimes directives A and B need to be coupled - by product spec.

When sorting changes, persist it. On load, restore it. These two need to talk.

Instead of coupling them inside the grid, create a COORDINATOR directive. It hosts both directives and manages their interaction.

The grid stays dumb. The coupling is explicit and testable."

---

<p align="center">
  <img src="./assets/galaxy-brain.jpg" alt="Galaxy brain meme" width="400" />
</p>

---

### Slide 33 – The Decision Framework

**On screen:**

| Signal                  | Tool               |
| ----------------------- | ------------------ |
| Parent configures child | Inputs/Outputs     |
| Structural flags        | Content Projection |
| Behavioral bundles      | Strategy via DI    |
| Optional behaviors      | Directives         |
| Same combo 3x           | hostDirectives     |

**You say:**

"Here's everything together.

What is the code telling you?

Parent configures child? Inputs. Stay here as long as you can.

Flags that control structure - what DOM exists? Extract the @if, use content projection.

Flags that bring dependent inputs and services? That's a behavioral bundle - extract it to a strategy.

Clean, optional behaviors that just exist or don't? Directives.

Same combo three times? Name it with hostDirectives.

Each row is a response to a signal. Your job: notice the signal. Pick the tool."

---

### Slide 34 – Your Monday Morning

**On screen:**

```
Find your biggest component.
Count the inputs.
Find the if-branches.

Ask: "What is this telling me?"
```

**You say:**

"Here's your homework.

Monday morning. Find the scariest component in your codebase. Count the inputs. Find the if-statements that check context.

Ask yourself: what is this telling me? What tool does it point to?

You might not refactor it. But you'll see it differently."

---

### Slide 35 – The Takeaway

**On screen:**

```
Good abstractions aren't chosen.

They're discovered.
```

**You say:**

"Angular gives us the tools. Inputs, content projection, DI, directives, host directives.

Our job isn't to pick our favorite and force it everywhere.

Our job is to listen to the code. Notice the repetition. Recognize the patterns.

Good abstractions aren't chosen. They're discovered."

---

### Slide 36 – Thank You

**On screen:**

```
Dor Peled
@Knat-Dev

Questions?
```

**You say:**

"Thanks for listening. I'm Dor, Knat-Dev online.

Come find me if you wanna talk about Angular, decoupling, or why your component has 47 inputs.

Questions?"

---

## Timing Estimate

| Section                                  | Slides | Minutes       |
| ---------------------------------------- | ------ | ------------- |
| Intro + Story                            | 1-5    | 4             |
| Tool 1: Inputs                           | 6-9    | 3             |
| Structural Flag → Extract @if            | 10-11  | 1.5           |
| Tool 2: Content Projection               | 12-14  | 2             |
| Flags Never Travel Alone                 | 16     | 1             |
| Tool 3: Strategy + Provider Functions    | 15-22  | 5             |
| Tool 4: Directives + Strategy combo      | 23-28  | 5             |
| Promotion + hostDirectives + Coordinator | 29-32  | 4             |
| Framework + Close                        | 33-36  | 2             |
| **Total**                                |        | **~27.5 min** |

---

## Q&A Prep

**"Why not start with directives?"**

> "Because inputs are simpler. Simpler is better when it's sufficient. Only reach for complexity when you see a clear cue that demands it."

**"When do I promote to hostDirectives?"**

> "Two times is coincidence. Three times is a concept. Name it."

**"How do directives communicate?"**

> "Through the component's signals. The component is the mediator. Directives inject it, read its signals, react to changes."

**"Can a directive use a strategy?"**

> "Absolutely. The directive provides a default strategy, so it works out of the box. If a consumer needs different behavior, they override the token in their component. The directive picks it up through DI. Composable AND flexible."

**"When would I use a coordinator directive vs just using both directives?"**

> "When A and B need to talk. If they're independent - just add both. But if sorting needs to trigger persistence, or filtering needs to update selection - that's a product requirement. Make it explicit with a coordinator. It's testable, documented, and the coupling is visible."

**"What about performance?"**

> "Directives are just classes. Effects watching signals. No extra change detection, no extra DOM. If anything it's faster because dependencies are clearer."

**"Can I use this with existing components?"**

> "Absolutely. Start by extracting one behavior into a directive. Then another. You don't have to do it all at once."

---

## Key Differences from v8

| Item               | Change                                                                        |
| ------------------ | ----------------------------------------------------------------------------- |
| Slide 18 (new)     | Provider functions - bundle defaults, provide once, override where needed     |
| Slide 19           | God component - the real problem is owning behaviors you can't opt out of     |
| Slide 21           | Directives use DI tree (no directive-level providers - would block override)  |
| Decision framework | Added "Repeated provider setup → Provider functions"                          |
| DI hierarchy       | Explained "closest parent wins" pattern                                       |
| Core message       | God component = owns everything, can't opt out. Directives = opt-in behaviors |

## Key Differences from v10 (Full Mess Upfront)

| Item             | Change                                                                   |
| ---------------- | ------------------------------------------------------------------------ |
| Slide 4          | Month 6 now shows FULL mess: flags + dependent inputs + services         |
| Slide 9          | "The Signal" hints that truth is deeper than just flags                  |
| Slide 16         | "The Behavioral Bundle" is now the reveal of what was hinted             |
| Decision table   | Simplified: structural flags → projection, behavioral bundles → strategy |
| Story arc        | Systematically dismantle Month 6, tackling one piece at a time           |
| Audience journey | "I've seen this" → "hint: truth is deeper" → reveal → relief → "Monday"  |

---

Ready for rehearsal.
