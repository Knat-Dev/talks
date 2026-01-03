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
Month 6: "Why is this so hard to change?"
```

**You say:**

"So here's a story you've probably lived.

Month one, you build a component. It's clean. Two inputs, maybe an output. Life is good.

Month six, product wants a variation. And you realize... this thing is stuck. Every change breaks something else."

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

### Slide 4 – Month 6

**On screen:**

```typescript
// Month 6
@Component({ selector: 'app-data-grid' })
export class DataGridComponent<T> {
  data = input<T[]>([]);
  columns = input<ColumnDef<T>[]>([]);
  loading = input(false);
  sortable = input(false);
  filterable = input(false);
  editable = input(false);
  persistColumns = input(false);
  showContextMenu = input(false);
  // ... and it keeps growing
}
```

**You say:**

"Month six. Sortable, filterable, editable, persist columns, context menu...

And here's the thing - we didn't inherit this mess. We built it. One reasonable feature at a time."

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

### Slide 9 – The Smell: Boolean Flags

**On screen:**

```typescript
// The smell:
readonly isAdmin = input(false);
readonly isReadOnly = input(false);
readonly isCompactMode = input(false);

// Component checking who's using it:
if (this.isAdmin()) {
  // show delete button
}
if (this.isCompactMode()) {
  // hide some columns
}
```

**You say:**

"But here's the smell that inputs are breaking down.

Boolean flags. The component is asking 'who's using me?' and changing behavior based on the answer.

When you see if-branches based on context flags, inputs have outgrown their welcome."

---

### Slide 10 – Tool 2: Content Projection

**On screen:**

```
When STRUCTURE varies.

Not behavior. Structure.
```

**You say:**

"Content projection. The parent passes structure in, not configuration down.

Use this when the variation is about WHAT goes WHERE. Headers, footers, custom templates."

---

### Slide 11 – Content Projection: The Code

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

### Slide 12 – The Tell: Behavior, Not Structure

**On screen:**

```
Content projection works for:
  ✓ Headers and footers
  ✓ Custom row templates
  ✓ Action buttons

Doesn't work for:
  ✗ "Save to localStorage vs server"
  ✗ "Sort ascending vs descending by default"
  ✗ "Fetch data this way vs that way"
```

**You say:**

"But here's the tell it's breaking down.

You need different BEHAVIOR, not different structure. Content projection can't help you there.

If you're trying to ng-content a function... you've gone too far."

---

### Slide 13 – Tool 3: Strategy via DI

**On screen:**

```
When behavior is exclusive.

A or B. Never both.
```

**You say:**

"Strategy pattern via dependency injection.

This is for mutually exclusive behavior. Exactly one implementation wins at runtime."

---

### Slide 14 – Strategy: The Problem

**On screen:**

```typescript
// The grid needs to persist column state
// But WHERE it persists depends on context:

// Admin dashboard → save to server
// Public view → save to localStorage
// Preview mode → don't save at all
```

**You say:**

"Let's say our grid persists column state. Width, order, visibility.

But where it saves depends on who's using it. Server for admins, localStorage for regular users, nowhere for previews.

Three different behaviors. The grid shouldn't know which one."

---

### Slide 15 – Strategy: The Interface

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

### Slide 16 – Strategy: The Implementations

**On screen:**

```typescript
// Implementation A: localStorage
export class LocalStorageStrategy implements StorageStrategy {
  save(key: string, data: unknown) {
    localStorage.setItem(key, JSON.stringify(data));
  }
  load(key: string) {
    return JSON.parse(localStorage.getItem(key) ?? 'null');
  }
}

// Implementation B: Server
export class ServerStorageStrategy implements StorageStrategy {
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

### Slide 17 – Strategy: The Provider

**On screen:**

```typescript
// Context A: Admin dashboard
@Component({
  providers: [
    { provide: STORAGE_STRATEGY, useClass: ServerStorageStrategy }
  ]
})
export class AdminDashboard {}

// Context B: Public view
@Component({
  providers: [
    { provide: STORAGE_STRATEGY, useClass: LocalStorageStrategy }
  ]
})
export class PublicView {}
```

**You say:**

"The decision lives in the provider. Same grid component, different storage behavior.

Zero if-statements in the grid. The context decides, not the component."

---

### Slide 18 – Provider Functions

**On screen:**

```typescript
// Bundle defaults into a reusable function
export function provideStorageDefaults(): Provider[] {
  return [
    { provide: STORAGE_STRATEGY, useClass: LocalStorageStrategy }
  ];
}

// Provide once at app or feature level
bootstrapApplication(AppComponent, {
  providers: [provideStorageDefaults()]
});

// Override only where needed
@Component({
  providers: [
    { provide: STORAGE_STRATEGY, useClass: ServerStorageStrategy }
  ]
})
export class AdminDashboard {}
```

**You say:**

"Pro tip - bundle your defaults into a provider function.

Call it once at the top. Everything below gets the default. Override only where you need something different.

DI hierarchy does the rest. Closest parent wins."

---

### Slide 19 – The Cue: God Component

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

### Slide 20 – Tool 4: Directive Composition

**On screen:**

```
When behaviors ACCUMULATE.

A and B and C.
```

**You say:**

"Directive composition.

Each behavior is a directive. They don't replace each other - they stack. They compose by addition."

---

### Slide 21 – Directives: The Code

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

### Slide 22 – Directives: Override When Needed

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

### Slide 23 – Directives: The Usage

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

### Slide 24 – Directive Communication

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

### Slide 25 – The Sign: Repeated Patterns

**On screen:**

```html
<!-- Page A -->
<app-data-grid sortable filterable persistColumns contextMenu />

<!-- Page B -->
<app-data-grid sortable filterable persistColumns contextMenu />

<!-- Page C -->
<app-data-grid sortable filterable persistColumns contextMenu />
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

### Slide 26 – The Promotion Rule

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

### Slide 27 – hostDirectives

**On screen:**

```typescript
@Directive({
  selector: '[simpleGrid]',
  hostDirectives: [
    SortableDirective,
    FilterableDirective,
  ],
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

### Slide 28 – Before/After

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
<app-data-grid
  powerGrid
  [data]="items"
/>
```

**You say:**

"Four attributes become one.

But more importantly - the pattern has a name now. 'Power grid' is a thing in your codebase. It's not just 'grid with these four directives'."

---

<p align="center">
  <img src="./assets/pam-theyre-different.jpg" alt="Pam from The Office meme" width="400" />
</p>

---

### Slide 29 – Coordinator Directive

**On screen:**

```typescript
// Sorting and persistence need to work together:
// When sort changes → persist the new sort
// On init → restore persisted sort

@Directive({
  selector: 'app-data-grid[persistedSort]',
  hostDirectives: [SortableDirective, PersistColumnsDirective],
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

### Slide 30 – The Decision Framework

**On screen:**

| Cue | Tool |
|-----|------|
| Parent configures child | Inputs/Outputs |
| Boolean flags, if-branches | Time to graduate |
| Structure varies | Content Projection |
| Behavior A or B | Strategy via DI |
| Repeated provider setup | Provider functions |
| Behaviors A and B and C | Directives |
| Directive needs flexibility | Strategy inside directive |
| Same combo 3x | hostDirectives |
| A and B must be coupled | Coordinator directive |

**You say:**

"Here's the mental model on one slide.

It's not 'which pattern is best'. It's 'what is the code telling me right now?'

Each row is a response to a cue. You don't pick upfront. You listen."

---

### Slide 31 – Your Monday Morning

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

### Slide 32 – The Takeaway

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

### Slide 33 – Thank You

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

| Section | Slides | Minutes |
|---------|--------|---------|
| Intro + Story | 1-5 | 4 |
| Tool 1: Inputs | 6-9 | 3 |
| Tool 2: Content Projection | 10-12 | 2 |
| Tool 3: Strategy + Provider Functions | 13-19 | 5 |
| Tool 4: Directives + Strategy combo | 20-25 | 5 |
| Promotion + hostDirectives + Coordinator | 26-29 | 4 |
| Framework + Close | 30-33 | 2 |
| **Total** | | **~25 min** |

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

| Item | Change |
|------|--------|
| Slide 18 (new) | Provider functions - bundle defaults, provide once, override where needed |
| Slide 19 | God component - the real problem is owning behaviors you can't opt out of |
| Slide 21 | Directives use DI tree (no directive-level providers - would block override) |
| Decision framework | Added "Repeated provider setup → Provider functions" |
| DI hierarchy | Explained "closest parent wins" pattern |
| Core message | God component = owns everything, can't opt out. Directives = opt-in behaviors |

---

Ready for rehearsal.
