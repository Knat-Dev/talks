---
theme: ./theme
title: Decoupling in Angular
info: |
  ## Decoupling in Angular
  Letting the Code Tell You What It Needs

  Dor Peled · @Knat-Dev

  Runtime: ~25 min content + 5 min Q&A = 30 min total
layout: a-main-cover-2
highlighter: shiki
transition: slide-left
mdc: true
---

# Decoupling in Angular

## Letting the Code Tell You What It Needs

<template #right>
  <img src="/assets/qr-slides.png" class="w-48 rounded-lg shadow-lg border-4 border-white" />
  <div class="text-center mt-4">
    <div class="text-xl font-bold text-white">slides.knat.dev</div>
    <div class="text-white/90 text-sm mt-1">Follow along</div>
  </div>
</template>

<div class="absolute bottom-[30px] left-[80px] flex items-center gap-3">
  <img src="/assets/profile.jpg" class="w-24 h-24 rounded-full object-cover border-2 border-white/50" />
  <div class="text-white/80 text-2xl flex items-center gap-1">Dor Peled <span class="opacity-50">·</span> <span class="text-xl opacity-90">@Knat-Dev</span></div>
</div>

<!--
[0:00 - 0:25] (25s)

• Greeting + hook

• QR code to follow along

• Transition to about me
-->

---
layout: default
---

<template #title>

# About Me

</template>

<div class="flex items-center gap-12">
  <!-- Avatar -->
  <img src="/assets/profile.jpg" class="w-40 h-40 rounded-full object-cover border-4 shrink-0" style="border-color: var(--cx-green);" alt="Dor Peled" />

  <!-- Name & Role -->
  <div class="shrink-0">
    <div class="font-bold text-2xl text-gray-800 flex items-center gap-2">Dor Peled <span class="text-gray-600 font-normal">·</span> <span class="text-lg font-normal text-gray-700">@Knat-Dev</span></div>
    <div class="text-lg text-gray-600">Software Engineer @ Coralogix</div>
    <div class="text-sm text-gray-700 mt-1">
      Angular, large codebases, and hard trade-offs
    </div>
    <div class="flex gap-4 mt-4 text-lg">
      <a href="https://github.com/Knat-Dev" target="_blank" class="text-gray-600 hover:text-gray-800"><carbon-logo-github /></a>
      <a href="https://www.linkedin.com/in/knat-dev" target="_blank" class="text-gray-600 hover:text-gray-800"><carbon-logo-linkedin /></a>
    </div>
  </div>

  <!-- Outside of work -->
  <div class="flex flex-col gap-3">
    <div class="flex items-center gap-3">
      <span class="text-2xl">🎸</span>
      <span class="text-gray-600">
        Playing guitar ~20 years (metal bands in the past)
      </span>
    </div>
    <div class="flex items-center gap-3">
      <span class="text-2xl">🤘</span>
      <span class="text-gray-600">Metal — taste, not background noise</span>
    </div>
    <div class="flex items-center gap-3">
      <span class="text-2xl">📚</span>
      <span class="text-gray-600">Fantasy & long-form worlds</span>
    </div>
    <div class="flex items-center gap-3">
      <span class="text-2xl">🖥️</span>
      <span class="text-gray-600">Homelab tinkering</span>
    </div>
  </div>
</div>

<!--
[0:25 - 0:55] (30s)

• Quick intro — Dor, Coralogix, Angular in large codebase

• Outside work — guitar, metal, fantasy

• Let's dive in
-->

---
layout: default
---

<template #title>

# Let's Talk Code

</template>

Who here has added _"just one more input"_ to a component?

One more boolean and that's it?

<!--
[0:55 - 1:50] (55s)

• Hands up: who added "just one more input"?

• Hands up: who waited for someone to finish editing the same file?

• That's how God Components are born

• Looking for "tells" — like in poker

• Let's see the component that taught me this
-->

---
layout: default
---

<template #title>

# The Grid

</template>

**Started simple...**

```ts
@Component({ selector: 'app-grid' })
export class GridComponent<T> {
  data = input<T[]>([]);
  loading = input(false);
  options = input<GridOptions<T> | null>(null);
}
```

<!--
[1:50 - 2:05] (15s)

• Before theory — let's see what actually happened

• Built a Grid component — started simple: data, loading, options
-->

---
layout: section
---

# Then Requirements Came...

Custom templates, row styles, expand/collapse...

## Context menus, column management, persistence...

<!--
[2:05 - 2:25] (20s)

• Requirements started coming in...

• Custom templates, row styles, expand/collapse, context menus

• Column management, persistence, global state deps...

• Each one "just one more feature"
-->

---
layout: center
---

<div class="text-center">
  <div class="text-2xl text-gray-700 mb-8">And then...</div>
  <div class="text-5xl font-bold">"Make it generic & reusable"</div>
  <div class="text-xl text-gray-600 mt-4">across the entire company</div>
</div>

<img src="/assets/this-is-fine.jpg" class="absolute bottom-8 right-8 w-80 rounded-lg shadow-xl" />

<!--
[2:25 - 2:50] (25s)

• The big one — every team wants to use it, but they all have slightly different needs

• Who maintained something like this? Let me tell you what happened
-->

---
layout: center
---

<div class="text-center">
  <div class="text-8xl mb-8">🧠</div>
  <div class="text-4xl font-bold text-red-400 mb-4">Cognitive Load</div>
  <div class="text-xl text-gray-600">Had to hold the entire Grid in your head to make any change</div>
</div>

<!--
[2:50 - 3:05] (15s)

• Cognitive Load — hold 2000 lines in your head for any change
-->

---
layout: center
---

<div class="text-center">
  <div class="text-8xl mb-8">🚧</div>
  <div class="text-4xl font-bold text-red-400 mb-4">Bottleneck</div>
  <div class="text-xl text-gray-600">Only 2 people "understood" it. Everyone else was afraid to touch it</div>
</div>

<!--
[3:05 - 3:20] (15s)

• Bottleneck — only 2 people "understood" it, rest afraid to touch
-->

---
layout: center
---

<div class="text-center">
  <div class="text-8xl mb-8">🐌</div>
  <div class="text-4xl font-bold text-red-400 mb-4">Slow Delivery</div>
  <div class="text-xl text-gray-600">Simple features took weeks. Bugs hid in the complexity</div>
</div>

<!--
[3:20 - 3:35] (15s)

• Slow Delivery — simple features took weeks, bugs hid in complexity
-->

---
layout: center
---

<div class="text-center text-3xl">
  <span class="text-gray-600">Technical debt</span> <span class="text-red-400 font-bold">compounds</span><span class="text-gray-600">.</span>
  <div class="mt-6 text-xl text-gray-700">Every shortcut today is a tax on every change tomorrow.</div>
</div>

<!--
[3:35 - 3:50] (15s)

• Technical debt compounds — every shortcut is a tax on tomorrow
-->

---
layout: default
---

<template #title>

# The Mega List

</template>

```ts [list.ts]
export class ListComponent {
  items = input<Item[]>([]);

  showHeader = input(false);
  multiSelect = input(false);
  autoSave = input(false);
}
```

Three boolean flags. Looks innocent.

<!--
[3:50 - 4:05] (15s)

• Grid too big for slides — built smaller example with same problems

• Three boolean flags — looks innocent, right?

• But booleans never come alone...
-->

---
layout: default
---

<template #title>

# Booleans Bring Friends

</template>

```ts [list.ts]
// showHeader brings...
showHeader = input(false);
headerTitle = input('');
headerActions = input<Action[]>([]);
```

One flag → three inputs.

<!--
[4:05 - 4:20] (15s)

• showHeader brings headerTitle, headerActions

• One boolean → three inputs to configure
-->

---
layout: default
---

<template #title>

# Booleans Bring Friends

</template>

```ts [list.ts]
// multiSelect brings...
multiSelect = input(false);
selection = output<Item[]>();
#selectionService = inject(SelectionService);
```

One flag → output + service.

<!--
[4:20 - 4:35] (15s)

• multiSelect brings selection output, SelectionService

• Now we have external dependencies
-->

---
layout: default
---

<template #title>

# Booleans Bring Friends

</template>

```ts [list.ts]
// autoSave brings...
autoSave = input(false);
saveDelay = input(300);
onSaved = output<void>();
#storage = inject(StorageService);
```

One flag → config + output + service.

_"What features am I today?"_

<!--
[4:35 - 4:50] (15s)

• autoSave brings saveDelay, onSaved, StorageService

• God Component asks "what features am I today?" instead of just being a list
-->

---
layout: default
---

<template #title>

# God Component

</template>

> A component that knows too much or does too much. It violates the single responsibility principle by handling multiple concerns that should be separated.

Our **Mega List** is becoming one.

<!--
[4:50 - 5:05] (15s)

• God Component — knows too much, does too much

• Our Mega List is becoming one
-->

---
layout: section
---

# The Real Problem

Coupling isn't bad.

## Hidden coupling is.

<!--
[5:05 - 5:40] (35s)

• Coupling isn't bad — HIDDEN coupling is

• Visible deps (inputs, constructor) = manageable

• Buried in flags/templates = lose control, touch here, breaks there

• Question: not "remove coupling" but "make it VISIBLE"

• Inputs = your default, extraction patterns when they fail
-->

---
layout: section
---

# The Journey

From God Component to clean architecture

<!--
[5:40 - 5:50] (10s)

• Here's the map — five steps from God Component to clean architecture
-->

---
layout: center
---

<div class="text-center">
  <div class="text-6xl font-bold mb-4">1. Map</div>
  <div class="text-2xl text-gray-600">The <span style="color: var(--cx-green); font-weight: bold;">WHERE</span></div>
  <div class="text-xl text-gray-700 mt-4">Identify context</div>
</div>

<!--
[5:50 - 5:58] (8s)

• Map — what varies and where, shooting blind without it
-->

---
layout: center
---

<div class="text-center">
  <div class="text-6xl font-bold mb-4">2. Extract</div>
  <div class="text-2xl text-gray-600">The <span style="color: var(--cx-green); font-weight: bold;">WHAT</span></div>
  <div class="text-xl text-gray-700 mt-4">Content Projection</div>
</div>

<!--
[5:58 - 6:06] (8s)

• Extract — pull behaviors out of the component
-->

---
layout: center
---

<div class="text-center">
  <div class="text-6xl font-bold mb-4">3. Interface</div>
  <div class="text-2xl text-gray-600">The <span style="color: var(--cx-green); font-weight: bold;">HOW</span></div>
  <div class="text-xl text-gray-700 mt-4">Strategy via DI</div>
</div>

<!--
[6:06 - 6:14] (8s)

• Interface — define contracts for swappable behaviors
-->

---
layout: center
---

<div class="text-center">
  <div class="text-6xl font-bold mb-4">4. Compose</div>
  <div class="text-2xl text-gray-600">The <span style="color: var(--cx-green); font-weight: bold;">WHETHER</span></div>
  <div class="text-xl text-gray-700 mt-4">Directives</div>
</div>

<!--
[6:14 - 6:22] (8s)

• Compose — make behaviors optional with directives
-->

---
layout: center
---

<div class="text-center">
  <div class="text-6xl font-bold mb-4">5. Bundle</div>
  <div class="text-2xl text-gray-600">The <span style="color: var(--cx-green); font-weight: bold;">WHICH</span></div>
  <div class="text-xl text-gray-700 mt-4">hostDirectives</div>
</div>

<!--
[6:22 - 6:32] (10s)

• Bundle — group repeating patterns with hostDirectives

• Let's start with Map
-->

---
layout: default
---

<template #title>

# The Map (WHERE)

</template>

| Feature | Main Page | Admin Panel | Preview |
|---------|-----------|-------------|---------|
| items | ✓ | ✓ | ✓ |

**items** everywhere? That's the **baseline** — stays in component.

<!--
[6:32 - 6:45] (13s)

• Map tool — simple matrix: rows = features, columns = contexts

• items everywhere? baseline — stays in component
-->

---
layout: default
---

<template #title>

# The Map (WHERE)

</template>

| Feature | Main Page | Admin Panel | Preview |
|---------|-----------|-------------|---------|
| showHeader | ✓ | ✗ | ✓ |

**showHeader** varies? Signal for **Content Projection**.

<!--
[6:45 - 6:55] (10s)

• showHeader varies? signal for Content Projection
-->

---
layout: default
---

<template #title>

# The Map (WHERE)

</template>

| Feature | Main Page | Admin Panel | Preview |
|---------|-----------|-------------|---------|
| selection | Single | Multi | None |

**selection** varies in **type** — not just on/off. Signal for **Strategy**.

<!--
[6:55 - 7:10] (15s)

• selection varies — but not boolean! Single, Multi, or None

• Different logic, not just toggle — needs Strategy pattern
-->

---
layout: default
---

<template #title>

# The Map (WHERE)

</template>

| Feature | Main Page | Admin Panel | Preview |
|---------|-----------|-------------|---------|
| sortable | ✓ | ✓ | ✗ |

**sortable** varies — opt-in behavior. Signal for **Directives**.

<!--
[7:10 - 7:20] (10s)

• sortable varies — optional behavior, use Directives
-->

---
layout: default
---

<template #title>

# The Map (WHERE)

</template>

| Feature | Main Page | Admin Panel | Preview |
|---------|-----------|-------------|---------|
| autoSave | ✗ | ✓ | ✗ |

**autoSave** only in one place? **Doesn't belong in component.**

<!--
[7:20 - 7:35] (15s)

• autoSave only in Admin — doesn't belong in component

• Now we have a map — we know what to extract
-->

---
layout: default
---

<template #title>

# The Tell

</template>

```ts [list.ts] {1-3}
sortable = input(false);
showHeader = input(false);
persistState = input(false);
```

Boolean flags. The component asking _"what features am I?"_

But look closer at **The Mega List**...

<!--
[7:35 - 8:10] (35s)

• sortable, showHeader, persistState — each is a feature toggle

• Component asking "what do you want me to be today?"

• The Tell — like poker, pile of booleans = component trying to be everything

• Notice: storageKey + initialState only relevant when persistState=true — they travel together

• One of these flags is an odd bird...
-->

---
layout: default
---

<template #title>

# The Structural Flag

</template>

```ts [list.ts]
// From The Mega List:
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
[8:10 - 8:50] (40s)

• showHeader is different — controls STRUCTURE, not behavior

• @if in template decides if Header renders

• Next request: "show icon only on Thursdays", "header only for US"...

• How many inputs? How many @ifs until it breaks?

• Component asks "how should I look?" — not its job

• Why should the list decide if it has a header?

• Let's extract the @if
-->

---
layout: default
---

<template #title>

# Extract the @if

</template>

```html [before]
<!-- Before: component decides structure -->
<app-list [showHeader]="true" />
```

```html [after]
<!-- After: parent decides structure -->
<app-list>
  <app-list-header header />
</app-list>
```

The **@if** becomes a **slot**. The boolean disappears.

<!--
[8:50 - 9:30] (40s)

• Before: pass true, component renders internally

• After: no boolean, parent just... puts header there, or doesn't

• @if becomes ng-content — boolean evaporates

• Content projection = what happens when you stop making structural decisions inside

• Aha moment: projected content is just an @if you pulled out

• Not every @if — only structural ones. Loading states, null guards stay inside
-->

---
layout: section
---

<template #title>

# Tool 1: Content Projection

</template>

Separating the <span style="color: var(--cx-green); font-weight: bold;">WHAT</span>

What content appears. Not how it behaves.

<!--
[9:30 - 9:50] (20s)

• Tool 1: Content Projection — separates the WHAT

• When inputs control STRUCTURE (what DOM exists)

• Like a picture frame — frame owns size/shape, you choose the picture

• Component owns layout, you own content
-->

---
layout: section
---

# The Shortcut

"Just one more @if"

<!--
[9:50 - 9:58] (8s)

• The familiar excuse — just add one more conditional

• Seems harmless in the moment
-->

---
layout: section
---

# The Tax

Template spaghetti.

## Impossible to test.

<!--
[9:58 - 10:06] (8s)

• Reality: template becomes unreadable

• Testing requires mocking every branch
-->

---
layout: default
---

<template #title>

# The Next Ceiling

</template>

Content Projection solved **WHAT** appears.

But what about **HOW** it behaves?

- _"Single select vs multi select"_
- _"Click to select vs checkbox"_

<!--
[10:06 - 10:40] (34s)

• Good move — extracted structural decision, boolean gone, parent decides

• But success revealed a new ceiling

• Content Projection solves WHAT — what appears

• But what about HOW — how things are done?

• Selection logic: single vs multi — completely different algorithms

• Need a tool that separates the HOW
-->

---
layout: default
---

<template #title>

# The Implementation Branch

</template>

```ts [list.ts]
select(item: Item) {
  if (this.multiSelect()) {
    // Multi: toggle in set
    this.selection.update(s => toggle(s, item));
  } else {
    // Single: clear and set
    this.selection.set(new Set([item]));
  }
}
```

The component knows **too much** about the "how".

<!--
[10:40 - 11:05] (25s)

• if multiSelect → toggle logic, else → replace logic

• Component knows all selection algorithms — knows too much

• What happens when you add range select? Checkbox mode?

• More else-if? And another?

• The tell: if-else on implementations = Strategy
-->

---
layout: section
---

<template #title>

# Tool 2: Strategy via DI

</template>

Separating the <span style="color: var(--cx-green); font-weight: bold;">HOW</span>

How it's done. Single or Multi, never both.

<!--
[11:05 - 11:25] (20s)

• Tool 2: Strategy via DI — separates the HOW

• A or B, pick one, never both together

• Single or Multi selection — only one runs

• Component doesn't know what it got — just asks "give me something that selects"
-->

---
layout: section
---

# The Shortcut

"Just add an if for multi-select"

<!--
[11:25 - 11:33] (8s)

• The familiar excuse — just check the mode

• One condition, what's the harm?
-->

---
layout: section
---

# The Tax

if-forests.

## Untestable without mocks.

<!--
[11:33 - 11:41] (8s)

• Reality: conditions multiply

• Every test needs to mock every branch
-->

---
layout: image-right
---

::left::

# Strategy: The Problem

That **if-else** needs to disappear. But where does the decision go?

::default::

| Context      | Selection |
| ------------ | --------- |
| Main Page    | Single    |
| Admin Panel  | Multi     |
| Preview      | None      |

<!--
[11:41 - 12:00] (19s)

• if-else needs to disappear from component — but where to?

• Depends on context: Main=single, Admin=multi, Preview=none

• Same component, completely different selection behavior

• Before: more ifs, more booleans. Now: component doesn't care
-->

---
layout: default
---

<template #title>

# Strategy: The Interface

</template>

```ts [selection-strategy.ts]
export interface SelectionStrategy {
  select(item: Item, current: Set<string>): Set<string>;
}

export const SELECTION_STRATEGY =
  new InjectionToken<SelectionStrategy>('SelectionStrategy');
```

<!--
[12:00 - 12:20] (20s)

• Interface: defines WHAT (select), not HOW

• InjectionToken = our key for DI

• Takes current selection, returns new selection

• Just the contract, no implementation yet
-->

---
layout: default
---

<template #title>

# Strategy: SingleSelection

</template>

```ts [single-selection.ts]
export class SingleSelection implements SelectionStrategy {
  select(item: Item, current: Set<string>) {
    // Clear everything, select only this one
    return new Set([item.id]);
  }
}
```

Click = replace. Only one item selected.

<!--
[12:20 - 12:35] (15s)

• Single selection — click replaces current selection

• Simple, predictable behavior
-->

---
layout: default
---

<template #title>

# Strategy: MultiSelection

</template>

```ts [multi-selection.ts]
export class MultiSelection implements SelectionStrategy {
  select(item: Item, current: Set<string>) {
    const next = new Set(current);
    // Toggle: add if missing, remove if present
    next.has(item.id) ? next.delete(item.id) : next.add(item.id);
    return next;
  }
}
```

Click = toggle. Multiple items selected.

**Same interface. Different "how".**

<!--
[12:35 - 12:55] (20s)

• Multi selection — click toggles item in set

• Same interface exactly — list calls select, doesn't know which strategy
-->

---
layout: default
---

<template #title>

# Strategy: The Provider

</template>

```ts [admin-panel.ts]
@Component({
  providers: [
    {
      provide: SELECTION_STRATEGY,
      useClass: MultiSelection,
    },
  ],
})
export class AdminPanel {}
```

**Zero if-statements.** Context decides, not component.

<!--
[12:55 - 13:40] (45s)

• Decision lives in PROVIDER

• AdminPanel says: "below me, anyone asking for Selection gets MultiSelection"

• Angular's power: hierarchical injection — affects only subtree

• Zero if-statements in component

• Pro tip: NoopSelection at root — nothing breaks by default, override where needed

• Strategy = exclusive choice. Composition = Directives

• This is VISIBLE coupling
-->

---
layout: default
---

<template #title>

# The Next Ceiling

</template>

```ts [list.ts]
export class ListComponent {
  #selection = inject(SELECTION_STRATEGY);
  #sorter = inject(SORT_STRATEGY);
  #filter = inject(FILTER_STRATEGY);
  // ... and 5 more tokens
}
```

**Too many tokens.** Strategy solved **HOW**. But what about **WHETHER**?

<img src="/assets/one-does-not-simply.jpg" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-120 rounded-lg shadow-xl" />

<!--
[13:40 - 14:25] (45s)

• Strategy worked — HOW is out, zero ifs

• But success revealed something new

• 3 tokens here, 5 more off-slide — component injects ALL, always, even when not needed

• What if sorting is optional? Filtering not relevant?

• [click meme]

• Main problem: can't see in template what's on

• New dev opens file — no clue what this list does without reading code

• Strategy solved HOW — need tool for WHETHER, and it must be VISIBLE
-->

---
layout: section
---

<template #title>

# Tool 3: Directives

</template>

Separating the <span style="color: var(--cx-green); font-weight: bold;">WHETHER</span>

Is it on or off? Composable opt-ins.

<!--
[14:25 - 14:45] (20s)

• Tool 3: Directives — separates the WHETHER

• Recap: Content Projection = WHAT, Strategy = HOW

• Directives fix WHETHER — injection moves to directive

• No directive? No injection. Exists or doesn't
-->

---
layout: section
---

# The Shortcut

"Just inject it everywhere"

<!--
[14:45 - 14:53] (8s)

• The familiar excuse — inject all services, check flags later

• Easier than thinking about what's actually needed
-->

---
layout: section
---

# The Tax

Hidden features.

## Null-check hell.

<!--
[14:53 - 15:01] (8s)

• Reality: can't tell what's active without reading code

• Every service needs null checks and guards
-->

---
layout: default
---

<template #title>

# Directive: Persistable

</template>

```ts [persistable.ts]
@Directive({ selector: 'app-list[persistable]' })
export class Persistable {
  #list = inject(ListComponent);
  #storage = inject(STORAGE_STRATEGY);
  storageKey = input.required<string>();

  constructor() {
    effect(() => {
      const state = this.#list.state();
      this.#storage.save(this.storageKey(), state);
    });
  }
}
```

Directive owns persistence. List doesn't know it's being saved.

<!--
[15:01 - 15:40] (39s)

• Selector: `app-list[persistable]` — no attribute = doesn't exist, zero overhead

• Remember the problem? List injected STORAGE_STRATEGY even when not needed

• Now: DIRECTIVE injects Strategy, not list

• List doesn't even know it's being saved

• Directive owns storageKey, listens to state changes, saves

• effect auto-cleans on directive destroy
-->

---
layout: default
---

<template #title>

# Directives: Usage

</template>

```html [app.html]
<!-- Simple -->
<app-list [items]="data" />

<!-- With persistence -->
<app-list persistable storageKey="admin-list" [items]="data" />

<!-- Full-featured -->
<app-list sortable sortKey="date" sortDir="desc"
          filterable persistable storageKey="main" [items]="data" />
```

**Visible in the template.** Look at the HTML, know what it does.

<!--
[15:40 - 16:20] (40s)

• Simple list? Nothing. Zero unnecessary injections

• Want persistence? Add `persistable` — directive brings storageKey

• STORAGE_STRATEGY injected ONLY when persistable exists

• Simple list? No storage service. No null checks

• Everything visible — new dev sees EXACTLY what this list does

• Each directive independent — combine in any order, each page picks what it needs

• This is the visibility payoff
-->

---
layout: default
---

<template #title>

# The Sign

</template>

```html [scattered across pages]
<!-- Page A -->
<app-list sortable filterable persistable [items]="a" />

<!-- Page B -->
<app-list sortable filterable persistable [items]="b" />

<!-- Page C -->
<app-list sortable filterable persistable [items]="c" />
```

Same combo. Three times.

<img src="/assets/distracted-boyfriend.jpg" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-120 rounded-lg shadow-xl" />

<!--
[16:20 - 17:00] (40s)

• Three different pages, exact same combo — copy-paste

• Coupling hides inside repetition: "we always put these three together"

• Want to add a fourth? Hunt through entire app. Miss one? Drift begins

• [click meme]

• Same thing 3 times? Not coincidence — concept without a name yet
-->

---
layout: section
---

<template #title>

# The Promotion Rule

</template>

Two times is coincidence.

**Three times is a concept.**

## Name it.

<!--
[17:00 - 17:40] (40s)

• Once = code. Twice = coincidence. Three times = concept — NAME IT

• Caveat: must be same REASON. Different reasons? Don't combine — false duplication

• With a name: becomes entity, can document, test, discuss in daily

• Without name: things drift. Saw 3 "identical" lists, one had sorting off "temporarily", 6 months later nobody remembered why

• Name it
-->

---
layout: default
---

<template #title>

# hostDirectives

</template>

```ts [power-list.ts]
@Directive({
  selector: 'app-list[powerList]',
  hostDirectives: [
    { directive: Sortable, inputs: ['sortKey', 'sortDir'] },
    { directive: Filterable, inputs: ['filterKey'] },
    Persistable,
  ],
})
export class PowerList {}
```

Forward inputs explicitly. **No magic.**

<!--
[17:40 - 18:20] (40s)

• Angular has `hostDirectives`

• PowerList bundles all three — one attribute brings the whole package

• Trade-off: lose template visibility, gain named concept + single update point

• No magic — input forwarding is explicit

• See `inputs: ['sortKey', 'sortDir']`? That's forwarding

• Persistable without forwarding = fixed preset, no knobs

• Intentional. You choose what to expose

• Want to add fourth behavior? One place
-->

---
layout: default
---

<template #title>

# Before/After

</template>

```html [app.html]
<!-- Before: 3 attributes -->
<app-list sortable filterable persistable [items]="data" />

<!-- After: 1 named concept -->
<app-list powerList [items]="data" />
```

<img src="/assets/pam-theyre-different.jpg" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-120 rounded-lg shadow-xl" />

<!--
[18:20 - 18:40] (20s)

• Before: grocery list of attributes. After: `powerList`

• [click meme]

• Code runs same thing. In your head? Different world

• New dev joins, sees `powerList` — understands immediately

• Turned a list of things into a concept with meaning
-->

---
layout: default
---

<template #title>

# Coordinator Directive

</template>

```ts [auto-save.ts]
@Directive({
  selector: 'app-list[autoSave]',
  hostDirectives: [StateTracker, Debouncer, LocalStorage],
})
export class AutoSaveCoordinator {
  #tracker = inject(StateTracker);   // 1. Emits changes
  #wait = inject(Debouncer);         // 2. Handles timing
  #disk = inject(LocalStorage);      // 3. Handles writing

  constructor() {
    // The Glue: Connect A → B → C
    effect(() => {
      const state = this.#tracker.changes();
      this.#wait.run(() => this.#disk.save(state));
    });
  }
}
```

The **Glue**. When A, B, and C **must** work together.

<!--
[18:40 - 19:15] (35s)

• Last pattern: Coordinator — the glue

• Product asks: "auto-save, but only when state changes, with debounce"

• Three separate concerns: tracking, timing, storage — they don't know each other

• Coordinator connects them: "when state changes → wait → save"

• This IS coupling — but intentional. Give it a home, a name, write a test
-->

---
layout: center
---

<div class="text-center">
  <div class="text-gray-600 text-sm uppercase tracking-widest mb-6">The Journey</div>
  <div class="flex justify-center gap-4">
    <div class="px-4 py-2 rounded-full text-sm font-semibold shadow-lg" style="background: var(--cx-green); color: #111827;">
      ✓ Map
    </div>
    <div class="px-4 py-2 rounded-full text-sm font-semibold shadow-lg" style="background: var(--cx-green); color: #111827;">
      ✓ Extract
    </div>
    <div class="px-4 py-2 rounded-full text-sm font-semibold shadow-lg" style="background: var(--cx-green); color: #111827;">
      ✓ Interface
    </div>
    <div class="px-4 py-2 rounded-full text-sm font-semibold shadow-lg" style="background: var(--cx-green); color: #111827;">
      ✓ Compose
    </div>
    <div class="px-4 py-2 rounded-full text-sm font-semibold shadow-lg" style="background: var(--cx-green); color: #111827;">
      ✓ Bundle
    </div>
  </div>
</div>

<!--
[19:15 - 19:22] (7s)

• Five steps — journey complete
-->

---
layout: center
---

<div class="text-center">
  <div class="text-8xl mb-8">🎯</div>
  <div class="text-4xl font-bold mb-4">Clear Scope</div>
  <div class="text-xl text-gray-600">Know exactly what to extract before starting</div>
</div>

<!--
[19:22 - 19:30] (8s)

• Clear scope — know exactly what to extract before starting
-->

---
layout: center
---

<div class="text-center">
  <div class="text-8xl mb-8">⚡</div>
  <div class="text-4xl font-bold mb-4">Parallel Work</div>
  <div class="text-xl text-gray-600">One dev on list, another on header — no waiting</div>
</div>

<!--
[19:30 - 19:38] (8s)

• Parallel work — one dev on list, another on header, no waiting
-->

---
layout: center
---

<div class="text-center">
  <div class="text-8xl mb-8">🧪</div>
  <div class="text-4xl font-bold mb-4">Testable in Isolation</div>
  <div class="text-xl text-gray-600">Each part testable separately</div>
</div>

<!--
[19:38 - 19:46] (8s)

• Testable in isolation — each part testable separately
-->

---
layout: center
---

<div class="text-center">
  <div class="text-8xl mb-8">📍</div>
  <div class="text-4xl font-bold mb-4">Single Point of Change</div>
  <div class="text-xl text-gray-600">Add behavior? One place.</div>
</div>

<!--
[19:46 - 19:54] (8s)

• Single point of change — add behavior? one place
-->

---
layout: center
---

<div class="text-center">
  <div class="text-8xl mb-8">💬</div>
  <div class="text-4xl font-bold mb-4">Shared Vocabulary</div>
  <div class="text-xl text-gray-600">Say "powerList" in daily — everyone knows</div>
</div>

<!--
[19:54 - 20:05] (11s)

• Shared vocabulary — say "powerList" in daily, everyone knows

• Now let's set guardrails
-->

---
layout: section
---

<template #title>

# When NOT to Use These

</template>

Each tool has limits.

<!--
[20:05 - 20:15] (10s)

• When NOT to use? Let's set boundaries
-->

---
layout: center
---

<div class="text-center">
  <div class="text-gray-600 text-sm uppercase tracking-widest mb-6">Guardrail</div>
  <div class="text-4xl font-bold mb-6">Content Projection</div>
  <div class="text-2xl text-red-400">Don't use when you need behavior, not structure</div>
</div>

<!--
[20:15 - 20:25] (10s)

• Content Projection? Structure only. Not behavior
-->

---
layout: center
---

<div class="text-center">
  <div class="text-gray-600 text-sm uppercase tracking-widest mb-6">Guardrail</div>
  <div class="text-4xl font-bold mb-6">Strategy via DI</div>
  <div class="text-2xl text-red-400">Don't use when only one implementation will ever exist</div>
</div>

<!--
[20:25 - 20:35] (10s)

• Strategy? Not if only one implementation exists
-->

---
layout: center
---

<div class="text-center">
  <div class="text-gray-600 text-sm uppercase tracking-widest mb-6">Guardrail</div>
  <div class="text-4xl font-bold mb-6">Directives</div>
  <div class="text-2xl text-red-400">Don't use when behavior is exclusive (use Strategy instead)</div>
</div>

<!--
[20:35 - 20:45] (10s)

• Directives? Not for exclusive A-or-B choices
-->

---
layout: center
---

<div class="text-center">
  <div class="text-gray-600 text-sm uppercase tracking-widest mb-6">Guardrail</div>
  <div class="text-4xl font-bold mb-6">hostDirectives</div>
  <div class="text-2xl text-red-400">Don't use when things are unrelated — don't bundle them</div>
</div>

<!--
[20:45 - 20:58] (13s)

• hostDirectives? Bundle only related things

• Now let's go back to the beginning
-->

---
layout: section
---

<template #title>

# The Circle Closes

</template>

Remember the price we paid?

<!--
[20:58 - 21:40] (42s)

• Remember the price we paid?

• Cognitive Load — now each tool does one thing. Can hold in head

• Bottleneck — now everyone can work on their directive. No queue for same file

• Slow Delivery — now strategy change = one file. Not PR touching everywhere

• My metric: how many places to touch for small change. Went from "many" to "one"

• This wasn't about patterns

• It was about freeing the team

• Before we finish — here's the map to take home
-->

---
layout: section
---

# Decision Framework

Inputs are your default. When they fail:

**WHERE, WHAT, HOW, WHETHER, WHICH**

<!--
[21:40 - 21:52] (12s)

• Remember the journey? Map, Extract, Interface, Compose, Bundle

• Here's the summary — five questions: WHERE, WHAT, HOW, WHETHER, WHICH
-->

---
layout: center
---

<div class="text-center">
  <div class="text-gray-600 text-sm uppercase tracking-widest mb-4">The Tell</div>
  <div class="text-2xl mb-6">Cross-context drift</div>
  <div class="text-5xl font-bold mb-4" style="color: var(--cx-green);">Map Feature × Context</div>
  <div class="text-xl text-gray-600">Separates the <strong>WHERE</strong></div>
</div>

<!--
[21:52 - 22:02] (10s)

• Component drifting across contexts? Matrix — Map
-->

---
layout: center
---

<div class="text-center">
  <div class="text-gray-600 text-sm uppercase tracking-widest mb-4">The Tell</div>
  <div class="text-2xl mb-6">Structural flags</div>
  <div class="text-5xl font-bold mb-4" style="color: var(--cx-green);">Content Projection</div>
  <div class="text-xl text-gray-600">Separates the <strong>WHAT</strong></div>
</div>

<!--
[22:02 - 22:12] (10s)

• Boolean changing structure? Content Projection — Extract
-->

---
layout: center
---

<div class="text-center">
  <div class="text-gray-600 text-sm uppercase tracking-widest mb-4">The Tell</div>
  <div class="text-2xl mb-6">Exclusive alternatives</div>
  <div class="text-5xl font-bold mb-4" style="color: var(--cx-green);">Strategy via DI</div>
  <div class="text-xl text-gray-600">Separates the <strong>HOW</strong></div>
</div>

<!--
[22:12 - 22:22] (10s)

• Exclusive alternatives? Strategy — Interface
-->

---
layout: center
---

<div class="text-center">
  <div class="text-gray-600 text-sm uppercase tracking-widest mb-4">The Tell</div>
  <div class="text-2xl mb-6">Composable opt-ins</div>
  <div class="text-5xl font-bold mb-4" style="color: var(--cx-green);">Directives</div>
  <div class="text-xl text-gray-600">Separates the <strong>WHETHER</strong></div>
</div>

<!--
[22:22 - 22:32] (10s)

• Optional features? Directives — Compose
-->

---
layout: center
---

<div class="text-center">
  <div class="text-gray-600 text-sm uppercase tracking-widest mb-4">The Tell</div>
  <div class="text-2xl mb-6">Same combo 3x</div>
  <div class="text-5xl font-bold mb-4" style="color: var(--cx-green);">hostDirectives</div>
  <div class="text-xl text-gray-600">Separates the <strong>WHICH</strong></div>
</div>

<!--
[22:32 - 22:45] (13s)

• Copy-paste 3x? hostDirectives — Bundle

• Code talks to you. Your job: listen, pick right tool
-->

---
layout: section
---

<template #title>

# The Takeaway

</template>

Good abstractions aren't chosen.

## They're discovered.

<img src="/assets/qr-slides.png" class="absolute bottom-8 right-8 w-28 opacity-80" />

<!--
[22:45 - 23:55] (70s)

• "Good abstractions aren't chosen. They're discovered."

• Remember the Grid from the start? 2000 lines. 2 people understood. Everyone afraid to touch

• Now? Same Grid. But now it has clear boundaries. Each feature in its place. Any dev can touch their part

• Didn't happen because we read Design Patterns book

• Happened because we listened to code, spotted the Tells, extracted in time

• My challenge to you:

• Tomorrow morning, find one component

• Look at one boolean flag

• Ask: "who should really own this?"

• Don't change the code. Just think about it

• That's where discovery begins
-->

---
layout: end
---

# Thank You

<div class="flex items-center gap-6 mt-4">
  <img src="/assets/profile.jpg" class="w-20 h-20 rounded-full object-cover" alt="Dor Peled" />
  <div class="text-left">
    <div class="font-bold text-xl text-left flex items-center gap-2">Dor Peled <span class="font-normal opacity-50">·</span> <span class="text-base font-normal opacity-70">@Knat-Dev</span></div>
    <div class="opacity-80">Software Engineer @ Coralogix</div>
    <div class="flex gap-4 mt-3 text-lg">
      <a href="https://github.com/Knat-Dev" target="_blank" class="opacity-75 hover:opacity-100"><carbon-logo-github /></a>
      <a href="https://www.linkedin.com/in/knat-dev" target="_blank" class="opacity-75 hover:opacity-100"><carbon-logo-linkedin /></a>
    </div>
  </div>
</div>

<div class="text-left mt-4 text-3xl font-bold">Questions?</div>

<!--
[23:55 - 24:10] (15s)

• Thank you all

• Got a component fighting you? Let's talk. I'm here

• Questions?
-->
