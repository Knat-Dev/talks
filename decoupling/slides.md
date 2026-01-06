---
theme: ./theme
title: Decoupling in Angular
info: |
  ## Decoupling in Angular
  Letting the Code Tell You What It Needs

  Dor Peled · @Knat-Dev
layout: a-main-cover-2
highlighter: shiki
transition: slide-left
mdc: true
---

# Decoupling in Angular

## Letting the Code Tell You What It Needs

**Dor Peled** · @Knat-Dev

<!--
[0:00 - 1:00]

Hey everyone, I'm Dor. Software engineer at Coralogix.

Here's what we're going to do today. We're going to build a component together. Well - I'll write the code, but I want you to help me spot when something's off.

You know those moments when you look at code and think "this feels wrong, but I can't explain why"? That's what we're hunting for. The tells.

We all know the tools - inputs, outputs, content projection, DI, directives. Documentation teaches syntax. Pain teaches architecture. I want to show you the pain so you recognize it before it bites you.

So watch the code with me. When you see something that makes you uncomfortable - that's the tell.

[Scan the room - make it feel like we're about to do this together]
-->

---
layout: section
---

# About Me

**Dor Peled** · @Knat-Dev

Software Engineer @ Coralogix

_I tend to stay with hard things longer than is comfortable_

<!--
[1:00 - 2:00]

Quick bit about me.

I tend to stay with hard problems longer than is comfortable. When code gets messy, I don't bounce. I sit with it. Try to understand what's actually going on.

Angular codebases are the same. They don't fail immediately. They accumulate complexity quietly. One feature at a time. And if you don't stay with them long enough, you miss the tells.

So this talk isn't best practices. It's pattern recognition. I'll show you what I've learned to look for - and you tell me if you see it too.

[Keep this tight - 45 seconds]
-->

---
layout: default
---

# Day 1

```ts [list.ts]
@Component({ selector: 'app-list' })
export class ListComponent {
  items = input<Item[]>([]);
  loading = input(false);
}
```

Clean. Simple. Works great.

<!--
[2:00 - 2:45]

Alright, let's build something. A list component. Simple.

Day one. Two inputs - items, loading. Look at this code. What do you think?

Clean, right? Simple. Easy to test. This is good code.

And it IS good code. Right now, this is exactly the right abstraction.

Enjoy this moment. It won't last.

[Brief pause - let them appreciate the calm before the storm]
-->

---
layout: default
---

# Month 2

```ts [list.ts]
@Component({ selector: 'app-list' })
export class ListComponent {
  items = input<Item[]>([]);
  // ... loading, error, etc.
  sortable = input(false);        // flag
  showHeader = input(false);      // flag  
  persistState = input(false);    // flag + friends below

  storageKey = input<string>();   // only if persistState
  initialState = input<ListState>();
  #storage = inject(StorageService);
  // ... more services

  ngOnInit() {
    if (this.persistState()) { /* 30 lines... */ }
  }
}
```

<v-click>
<img src="/assets/this-is-fine.jpg" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-120 rounded-lg shadow-xl" />
</v-click>

<!--
[2:45 - 4:00]

Month two. Product shows up. "Can we add sorting?" Sure. "A header option?" Okay. "Remember state?" Fine.

Now look at this code. Really look.

Boolean flags - sortable, showHeader, persistState. Dependent inputs - storageKey, initialState. An output. Two injected services. Lifecycle code.

And this is month TWO. Imagine month six. Imagine year two.

We didn't inherit this mess. We built it. One reasonable feature request at a time.

[Click for meme]

Show of hands - who's worked on a component like this?

[Pause for hands - acknowledge them]

Yeah. Me too. This is where we're starting today. This is what we're going to systematically take apart.

[Set up the thesis]
-->

---
layout: section
---

# The Real Problem

Coupling isn't bad.

## Hidden coupling is.

<!--
[4:00 - 5:00]

Here's the thing. Coupling isn't bad. Your components HAVE to talk. Data flows. Events propagate. That's fine.

HIDDEN coupling is the problem.

When you can see dependencies - in inputs, in constructors, in imports - you can reason about them. Manage them.

But when coupling is buried in boolean flags? Scattered across templates? That's when you lose control. Change something here, something breaks over there.

So the question isn't "how do I remove coupling?" It's "how do I make coupling VISIBLE?"

That's what we're going to do. Inputs are your default - stay there as long as you can. But when inputs go wrong, you have three escape hatches. Each one makes a different kind of coupling explicit.

[Clear and confident - this is the core idea]
-->

---
layout: image-right
---

::left::

# Three Escape Hatches

Inputs are your default.

**When they fail, reach for these.**

::default::

| The Tell            | Escape Hatch       |
| ------------------- | ------------------ |
| Structural flags    | Content Projection |
| Behavioral bundles  | Strategy via DI    |
| Composable opt-ins  | Directives         |

<!--
[5:00 - 6:00]

Three escape hatches. You know all of them. The trick is recognizing WHEN to reach for each.

Inputs are your default. They create VISIBLE coupling - you can see what a component needs. Stay there as long as you can.

But watch for the tells.

Structural flags - booleans that control what DOM exists? Content projection. Extract the @if.

Behavioral bundles - a flag that drags along services and lifecycle code? Strategy via DI. Let the injector decide.

Composable opt-ins - behaviors that should stack? Directives. Visible in the template.

For each escape hatch, I'll show you the code, and we'll spot the tell together.

[Point to each row briefly - then move on]
-->

---
layout: default
---

# The Tell

```ts [list.ts] {1-3}
sortable = input(false);
showHeader = input(false);
persistState = input(false);
```

Boolean flags. The component asking _"what features am I?"_

But look closer at our Month 2 code...

<!--
[6:00 - 7:00]

Look at these. Boolean flags. sortable, showHeader, persistState.

Each one is a feature toggle. The component is asking: "What features am I running today?"

That's the tell - like in poker. When you see boolean flags accumulating, the component is trying to be too many things.

But here's the thing - and we'll come back to this - boolean flags are just the surface. Look back at the Month 2 code. Notice how storageKey and initialState only matter when persistState is true? They travel together. The truth is deeper than just flags.

For now, let's start with the simplest case. One of these flags isn't like the others.

[Pause - plant the seed, don't explain yet]
-->

---
layout: default
---

# The Structural Flag

```ts [list.ts]
// From our Month 2 component:
showHeader = input(false);
```

```html [list.html]
@if (showHeader()) {
  <app-header />
}
<div class="list-body">...</div>
```

Why does the **list** decide whether a header exists?

<!--
[7:00 - 7:30]

Let's start with showHeader. This one is different from sortable or persistState.

It doesn't change behavior - it controls structure. What DOM elements exist.

Look at this. showHeader. And somewhere in the template, an @if that renders a header.

Why does the LIST decide whether a header exists? That's not data. That's not behavior. That's the parent's concern.

Bonus: if the parent projects, the list doesn't need to import the header component. Cleaner dependency graph - the list doesn't need to know about the header's type at all.

This is the simplest extraction we can make.

[Let them feel the discomfort]
-->

---
layout: default
---

# Extract the @if

```html [before]
<!-- Before: component decides structure -->
<app-list [showHeader]="true" />
```

```html [after]
<!-- After: parent decides structure -->
<app-list>
  <app-header header></app-header>
</app-list>
```

The `@if` becomes a **slot**. The boolean disappears.

<!--
[7:30 - 8:00]

Watch what happens when you extract the conditional.

Before: showHeader equals true. Component renders the header internally.

After: No boolean. Parent just... puts the header there. Or doesn't.

The @if block becomes an ng-content slot. The boolean vanishes.

Content projection isn't a "layout feature". It's what happens when you extract structural decisions from components.

[This is the "aha" moment - content projection is removal of conditionals]
-->

---
layout: section
---

# Tool 1: Content Projection

When **structure** varies

Not behavior. Structure.

<!--
[8:00 - 8:30]

Tool one. Content projection.

Our first escape hatch. Use this when inputs control STRUCTURE - what DOM exists, not what it does.

Think picture frame. Frame decides shape and size. You choose the picture.

Component owns layout. You own content.

[Quick - set up the code]
-->

---
layout: default
---

# Content Projection

```html [card.html]
<div class="header">
  <ng-content select="[header]" />
</div>
<ng-content />
```

Card owns layout. Consumer owns content via ng-content.

<!--
[8:30 - 9:00]

Two ng-content slots. Header and default.

Component says: "I'll handle the wrapper. You decide what goes inside."

Cards, modals, panels. Shell is consistent, contents vary.

[Quick - code speaks for itself]
-->

---
layout: default
---

# The Limit

Doesn't work for:

- _"Save to localStorage vs server"_
- _"Persist state when flag is true"_

You need different **behavior**, not structure.

<!--
[9:00 - 9:30]

So we handled showHeader. That boolean is gone. One down.

But look back at Month 2. What about persistState? And its friends - storageKey, initialState, the services?

Can we ng-content an HttpClient? Can we put localStorage in a slot?

No. Content projection is for DOM nodes. Visual stuff.

When the variation is about WHAT HAPPENS, not WHAT APPEARS - that's a different tool.

[Transition to strategy]
-->

---
layout: section
---

# Tool 2: Strategy via DI

**A** or **B**

Never both.

<!--
[9:30 - 10:00]

Tool two. Strategy via DI.

A or B. Pick one. Never both at the same time.

Server OR localStorage. Real API OR mock. One implementation active.

Component doesn't know which. It just says "I need something that can save." DI provides the right one.

No if-statements in the component. The decision lives elsewhere.

Let me show you what this looks like.

[Set up the example]
-->

---
layout: default
---

# The Behavioral Bundle

```ts [list.ts] {1,3-5,7-8,10-13}
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
```

Remember I said the truth was deeper? **This is it.**

<!--
[10:00 - 10:45]

Remember earlier I said the truth is deeper than just boolean flags?

This is it. Look at our Month 2 component again - just the persistence part.

One flag - persistState. But it's not alone.

storageKey - WHERE to save. initialState - what to restore. stateSaved - notify when done.

Then the services. ApiService for server. StorageService for browser. And the lifecycle code.

All of this only matters if persistState is true. These things travel together. They're a behavioral unit.

And here's the key insight: you can't content-project behavior. You can't put a service in an ng-content slot.

So what do we do when a whole bundle of behavior needs to move?

[This is the reveal - set up Strategy]
-->

---
layout: image-right
---

::left::

# Strategy: The Problem

That whole bundle needs to **move**. But where?

::default::

| Context | Storage      |
| ------- | ------------ |
| Admin   | Server       |
| Public  | localStorage |
| Preview | Don't save   |

<!--
[10:45 - 11:15]

That whole bundle - the flag, the inputs, the services, the lifecycle code - needs to move OUT of the component.

But WHERE it goes depends on context.

Admin dashboard? Save to server. Public view? localStorage. Preview mode? Don't save at all.

Same component, different storage behavior.

Old way: another boolean flag, another if-statement. New way: the component doesn't know. Doesn't care.

[Quick - set up the solution]
-->

---
layout: default
---

# Strategy: The Interface

```ts [storage-strategy.ts]
export interface StorageStrategy {
  save(key: string, data: unknown): Promise<void>;
  load<T>(key: string): Promise<T | null>;
}

export const STORAGE_STRATEGY =
  new InjectionToken<StorageStrategy>('StorageStrategy');
```

<!--
[11:15 - 11:30]

Step one: define what we need. save() and load(). Not HOW - just that it CAN.

InjectionToken is our lookup key.

Note: I'm using Promise to keep slides clean. You can model this as Observable too - same pattern, different async primitive.

No implementation yet. Just the contract.

[Fast - setup code]
-->

---
layout: default
---

# Strategy: LocalStorage

```ts [local-storage.ts]
export class LocalStorageStrategy
  implements StorageStrategy
{
  async save(key: string, data: unknown) {
    localStorage.setItem(key, JSON.stringify(data));
  }
  async load<T>(key: string) {
    const item = localStorage.getItem(key);
    return item ? JSON.parse(item) as T : null;
  }
}
```

Uses localStorage. Works offline.

<!--
[11:30 - 11:45]

First implementation. LocalStorageStrategy.

Browser storage. Synchronous. Works offline.

[Quick - move to next]
-->

---
layout: default
---

# Strategy: Server

```ts [server-storage.ts]
export class ServerStorageStrategy
  implements StorageStrategy
{
  #api = inject(ApiService);

  async save(key: string, data: unknown): Promise<void> {
    await firstValueFrom(
      this.#api.post<void>('/api/preferences', { key, data })
    );
  }
  async load<T>(key: string): Promise<T | null> {
    return firstValueFrom(
      this.#api.get<T | null>(`/api/preferences/${key}`)
    );
  }
}
```

Uses HttpClient. Syncs across devices.

<!--
[11:45 - 12:15]

Second implementation. ServerStorageStrategy.

API call. Syncs across devices.

From the outside? Identical. List calls save() and load(). Doesn't know which one it got.

Could add NoopStrategy for preview. Same interface, does nothing.

[Keep moving]
-->

---
layout: default
---

# Strategy: The Provider

```ts [admin-dashboard.ts]
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

**Zero if-statements.** Context decides, not component.

<!--
[12:15 - 13:00]

Here's where the decision lives. In the provider.

AdminDashboard says: "In my subtree, use ServerStorageStrategy."

Key insight: this provider is scoped to the AdminDashboard component subtree - not global. That's the magic of hierarchical injection.

List injects STORAGE_STRATEGY. Gets the server version. Zero if-statements.

Different page? Provides LocalStorageStrategy. Same list, different behavior.

Pro tip: provide a NoopStorageStrategy at root, override where needed. Safe by default, customized per context.

```ts
// The third implementation - does nothing, safely
export class NoopStorageStrategy implements StorageStrategy {
  async save() {}
  async load() { return null; }
}
```

The list never changes. New mode? Write new strategy, provide it somewhere. List doesn't know, doesn't care.

Important: Strategy is for mutually exclusive implementations. One active at a time. If you want opt-in feature composition - adding behaviors, not swapping them - don't keep adding tokens. That's what directives are for.

THAT's visible coupling. You can see exactly what behavior each context gets.

[Emphasize: scoped providers, zero if-statements, visible coupling]
-->

---
layout: default
---

# The Cue

```ts [list.ts]
export class ListComponent {
  #storage = inject(STORAGE_STRATEGY);
  #sorter = inject(SORT_STRATEGY);
  #filter = inject(FILTER_STRATEGY);
}
```

Features invisible in template. All three always present.

<v-click>
<img src="/assets/one-does-not-simply.jpg" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-120 rounded-lg shadow-xl" />
</v-click>

<!--
[13:00 - 13:45]

But look at this code. What's the problem?

Three strategies injected. You can SWAP implementations. But the feature set is invisible in the template.

Yes, Angular has optional injection. But then your component is riddled with `if (this.sorter)` checks. You're checking if behaviors exist instead of composing them.

[Click for meme]

DI strategy is not the cleanest expression of opt-in composition. You end up with nullable dependencies or no-op defaults, and the feature set is no longer visible in the template.

We need behaviors that ACCUMULATE. Add what you want, skip what you don't. Visible in the template.

That's tool three.

[Transition]
-->

---
layout: section
---

# Tool 3: Directives

What's **left** after the right extractions.

The smallest unit of reusable behavior.

<!--
[13:45 - 14:30]

Tool three. Directives.

Look back at Month 2. We handled showHeader - that's content projection now. We handled persistState and its bundle - that's a strategy now.

What's left? sortable.

It doesn't control structure. It's not a bundle of dependent inputs and services. It's just... a behavior. Either the list is sortable, or it's not.

No branching. No context questions. Present or absent.

That's a directive. The smallest unit of reusable behavior that survived extraction.

Directives aren't "yet another tool." They're what's LEFT when you've done the other extractions right.

[Reframe - directives are inevitable, not arbitrary]
-->

---
layout: default
---

# Directive: Sortable

```ts [sortable.ts]
@Directive({ selector: 'app-list[sortable]' })
export class Sortable {
  #list = inject(ListComponent);

  constructor() {
    effect(() => {
      const items = this.#list.items(); // signal read
      const sorted = [...items].sort(this.#list.compareFn);
      this.#list.displayItems.set(sorted);
    });
  }
}
```

Only activates with the sortable attribute.

<!--
[14:30 - 15:00]

SortableDirective. Selector targets app-list with sortable attribute.

No attribute? Directive doesn't exist. Zero overhead.

Injects the component, adds behavior. Same element as app-list → inject(ListComponent) resolves the host instance.

Yes, the directive knows the component's internal API. That's okay - they're a team. The selector `app-list[sortable]` makes this explicit: this directive is designed for this component.

Note: in a real app, sort state (direction, column) would also be a signal - the effect reacts to both items AND sort config changes.

[Quick - show the pattern]
-->

---
layout: default
---

# Directive: Persist

```ts [persistable.ts]
@Directive({ selector: 'app-list[persistable]' })
export class Persistable {
  #list = inject(ListComponent);
  #storage = inject(STORAGE_STRATEGY);

  constructor() {
    effect(() => {
      const key = this.#list.storageKey();
      if (!key) return;
      const state = this.#list.state();   // internal signal
      if (!state.dirty) return;
      this.#storage.save(key, state);
    });
  }
}
```

Notice: injects STORAGE_STRATEGY. **Tools layer together.**

<!--
[15:00 - 15:30]

PersistDirective. Same pattern.

But notice - it injects STORAGE_STRATEGY. This is the key insight: we're decoupling persistence into two axes.

Strategy answers: WHERE and HOW do we store? Server vs localStorage vs noop.
Directive answers: WHEN do we store? On state changes, only when dirty.

Yes, this effect causes I/O. In production I route it through a debounced queue with cancellation. But the decomposition point is what matters here: WHERE lives in strategy, WHEN lives in directive.

Tools layer together. Each one has a single job. Easy to test alone, easy to compose.

[Point out strategy injection - this is the payoff]
-->

---
layout: default
---

# Directives: Usage

```html [app.html]
<!-- Simple -->
<app-list sortable [items]="data" />

<!-- Full-featured -->
<app-list sortable filterable persistable [items]="data" />
```

Add sortable, filterable, persist as needed.

<!--
[15:30 - 16:00]

In the template.

Simple page? Just sortable. Full-featured? All three.

Each page picks its combination. Opt-in complexity.

[Fast - template speaks for itself]
-->

---
layout: default
---

# The Sign

```html [scattered across pages]
<!-- Page A -->
<app-list sortable filterable persistable [items]="a" />

<!-- Page B -->
<app-list sortable filterable persistable [items]="b" />

<!-- Page C -->
<app-list sortable filterable persistable [items]="c" />
```

Same combo. Three times.

<v-click>
<img src="/assets/distracted-boyfriend.jpg" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-120 rounded-lg shadow-xl" />
</v-click>

<!--
[16:00 - 16:45]

But look at this code. What do you see?

Three pages. Same three directives. Copy-pasted.

The coupling is hidden in REPETITION. "We always use these three together."

What happens when we add a fifth? Hunt down every page. Update all of them. Miss one? Now they drift apart.

[Click for meme]

Same combination three times. That's not coincidence. That's a concept without a name.

[Set up the promotion rule]
-->

---
layout: section
---

# The Promotion Rule

Two times is coincidence.

**Three times is a concept.**

## Name it.

<!--
[16:45 - 17:30]

The promotion rule.

Once: just code.
Twice: maybe coincidence.
Three times: that's a concept. Give it a name.

Important caveat: three times with the SAME meaning and the SAME reason. If the reasons differ, don't bundle. That's false duplication.

When you name something, you can talk about it. Document it. Test it. Evolve it in ONE place.

Unnamed patterns drift. We had three "full-featured lists." One had filtering disabled "temporarily." Six months later? Nobody knew why.

See it three times with the same meaning? Name it.

[Deliver with conviction - this is memorable]
-->

---
layout: default
---

# hostDirectives

```ts [power-list.ts]
@Directive({
  selector: 'app-list[powerList]',
  hostDirectives: [
    Sortable,
    Filterable,
    Persistable,
  ],
})
export class PowerList {}
```

One attribute. Three behaviors.

<!--
[17:30 - 18:15]

Angular gives us hostDirectives.

PowerListDirective bundles three others. One attribute, three behaviors.

Look at the body. Empty. No logic. Just composition. It's a name for a pattern.

Add a fourth behavior? One file. Done.

[Show how naming creates maintainability]
-->

---
layout: default
---

# Before/After

```html [app.html]
<!-- Before: 3 attributes -->
<app-list sortable filterable persistable [items]="data" />

<!-- After: 1 named concept -->
<app-list powerList [items]="data" />
```

<v-click>
<img src="/assets/pam-theyre-different.jpg" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-120 rounded-lg shadow-xl" />
</v-click>

<!--
[18:15 - 18:45]

Before: three attributes. After: one word. powerList.

[Click for meme]

Functionally identical. Conceptually? Worlds apart.

New developer sees "powerList" - immediately understands what this list does.

First version is a list of things. Second is a concept.

[Brief - slide is clear]
-->

---
layout: default
---

# Coordinator Directive

```ts [auto-saveable.ts]
@Directive({
  selector: 'app-list[autoSaveable]',
  hostDirectives: [DirtyTrackable, Debounceable],
})
export class AutoSaveable {
  #dirty = inject(DirtyTrackable);
  #debounce = inject(Debounceable);

  constructor() {
    effect(() => {
      if (this.#dirty.isDirty()) {
        this.#debounce.run(() => this.save());
      }
    });
  }
}
```

When A and B **must** work together.

<v-click>
<img src="/assets/galaxy-brain.jpg" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-120 rounded-lg shadow-xl" />
</v-click>

<!--
[18:45 - 19:30]

One more pattern. Coordinator directive.

Remember the dirty guard in Persistable? That was the simple version. In real code, you also want debouncing, cancellation of in-flight saves, etc.

Product says: "auto-save when dirty, but debounce it."

DirtyTrackable and Debounceable are independent directives. Neither knows about the other.

AutoSaveable brings both in, coordinates them. When dirty, debounce the save.

[Click for meme]

Intentional coupling? Give it a home. One place. Named. Testable.

[Keep it brief - they get it]
-->

---
layout: section
---

# When NOT to Use These

Each tool has limits.

<!--
[19:30 - 19:45]

Quick guardrails. When NOT to use each escape hatch.

[Fast section]
-->

---
layout: default
---

# Anti-Patterns

| Escape Hatch       | Don't use when...                       |
| ------------------ | --------------------------------------- |
| Content Projection | You need behavior, not structure        |
| Strategy via DI    | Behaviors should be optional/composable |
| Directives         | Same bundle repeated without naming     |
| hostDirectives     | Bundling unrelated things to save typing|

<!--
[19:45 - 20:15]

Quick summary.

Important mental model: Inputs are CHEAP - easy to read and debug. Escape hatches are EXPENSIVE - more files, more indirection, debugging jumps across DI boundaries and directive composition.

Don't reach for Tool 3 when inputs still work. Complexity should earn its keep.

Content projection: structure only, not behavior.
Strategies: not when behaviors should be optional.
Directives: great, but don't repeat the same bundle across pages - promote it to hostDirectives.
hostDirectives: don't bundle unrelated things just to reduce typing. Bundle concepts that belong together.

[Crisp - they have the framework]
-->

---
layout: default
---

# Testing Impact

```ts [list.spec.ts]
// Inputs: Easy isolation
const fixture = TestBed.createComponent(List);
fixture.componentRef.setInput('data', mockData);

// Strategy: Mock the interface
TestBed.configureTestingModule({
  providers: [
    { provide: STORAGE_STRATEGY, useValue: mockStorage },
  ],
});

// Directives: Test with and without
const withSorting =
  '<app-list sortable [data]="items" />';
const withoutSorting = '<app-list [data]="items" />';
```

<!--
[20:15 - 20:45]

Testing impact - quick note.

Inputs: easiest. Set inputs, check outputs.

Strategies: mock the interface. Test component and implementations separately.

Directives: test base component alone, each directive alone, then combinations.

Pattern choice affects how you test. Composability extends to your test suite.

[Brief - practical but not the focus]
-->

---
layout: default
---

# Migration Path

From Month 2 mess to clean architecture:

1. **Extract** - Pull behaviors out of the god component
2. **Interface** - Define contracts for swappable behaviors
3. **Compose** - Make remaining behaviors optional with directives
4. **Name** - Bundle common patterns with hostDirectives

<!--
[20:45 - 21:30]

So you've got a Month 2 mess. How do you fix it?

One: Extract. Pull behaviors out. Don't worry about perfection. Just get them out of the god component.

Two: Interface. Which behaviors are mutually exclusive? Those become strategies.

Three: Compose. Remaining behaviors become optional directives.

Four: Name. See it three times? hostDirectives.

Don't do it all at once. One behavior at a time. Each extraction makes the component lighter.

[Practical - this is Monday morning advice]
-->

---
layout: image-right
---

::left::

# Decision Framework

Inputs are your default. When they fail:

::default::

| The Tell           | Escape Hatch       |
| ------------------ | ------------------ |
| Structural flags   | Content Projection |
| Behavioral bundles | Strategy via DI    |
| Composable opt-ins | Directives         |
| Same combo 3x      | hostDirectives     |

<!--
[21:30 - 22:15]

Here's everything together.

Inputs are your default. Stay there as long as you can. They create visible coupling - that's manageable.

But when you see these tells, reach for the escape hatch:

Structural flags - booleans that control what DOM exists? Content projection. Extract the @if.

Behavioral bundles - a flag that drags services and lifecycle code? Strategy via DI. Let the injector decide.

Composable opt-ins - behaviors that should stack, present or absent? Directives. Visible in template.

Same combo three times with the same meaning? Name it with hostDirectives.

Each row is a response to a tell. Your job: notice the tell. Pick the escape hatch.

[Pause - let them photograph it]
-->

---
layout: section
---

# The Takeaway

Good abstractions aren't chosen.

## They're discovered.

<!--
[22:15 - 23:00]

Final thought.

I told you at the start I stay with hard things longer than is comfortable. That discomfort is where the discovery happens.

We talk about "choosing the right abstraction" like there's a menu at project start. That's not how it works.

Good abstractions aren't chosen. They're discovered.

You write code. Watch it evolve. Notice where it struggles. The code tells you - through boolean flags, repetition, god components - what it needs.

Your job isn't to predict the future. It's to listen to the present. Notice the tells.

Don't rush to fix the messy component. Listen to it first. Next time you see isAdmin or isCompactMode... you'll know what the code is telling you.

Stay with your components long enough. The patterns reveal themselves.

[Eye contact - let this land]
-->

---
layout: end
---

# Thank You

**Dor Peled** · @Knat-Dev

Software Engineer @ Coralogix

Questions?

<!--
[23:30 - 24:00]

That's it. Thank you.

If you've got a component that's fighting back - come find me. Or if you just want to talk Angular, decoupling, or why your component has 47 inputs... I'm around.

Questions?

[Open, approachable - real learning happens now]
-->
