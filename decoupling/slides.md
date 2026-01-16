---
theme: ./theme
title: Decoupling in Angular
info: |
  ## Decoupling in Angular
  Letting the Code Tell You What It Needs

  Dor Peled · @Knat-Dev

  Runtime: ~2,700 words in speaker notes.
  120 wpm (with pauses) = ~22 min.
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
[0:00 - 0:20]

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
    <div class="font-bold text-2xl text-gray-800 flex items-center gap-2">Dor Peled <span class="text-gray-400 font-normal">·</span> <span class="text-lg font-normal text-gray-500">@Knat-Dev</span></div>
    <div class="text-lg text-gray-600">Software Engineer @ Coralogix</div>
    <div class="text-sm text-gray-500 mt-1">
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
[0:20 - 0:45]

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
[0:45 - 1:30]

• Hands up: who added "just one more input"?

• Hands up: who waited for someone to finish editing the same file?

• That's how Mega Components are born

• Looking for "tells" — like in poker

• Let's see the component that taught me this
-->

---
layout: default
---

<template #title>

# The Grid

</template>

<div class="grid grid-cols-2 gap-12 mt-8">
<div>

**Started simple...**

```ts
@Component({ selector: 'app-grid' })
export class GridComponent<T> {
  data = input<T[]>([]);
  loading = input(false);
  options = input<GridOptions<T> | null>(null);
}
```

</div>
<div>

**Then requirements came...**

<v-clicks>

- Custom templates
- Row styles
- Expand / Collapse
- Context menu
- Lazy loading
- Persistence
- Global state deps...
- "Make it generic & reusable"

</v-clicks>

</div>
</div>

<img v-click src="/assets/this-is-fine.jpg" class="absolute top-10 right-10 w-96 rounded-lg shadow-xl" />

<!--
[1:30 - 2:30]

• Before theory — let's see what actually happened

• Built a Grid component — started simple: data, loading, options

• Then requirements came... [click through each]

• [click] Who maintained something like this? Let me tell you what happened
-->

---
layout: default
---

<template #title>

# The Price We Paid

</template>

<div class="grid grid-cols-3 gap-6 mt-8">

<div class="text-center">
<div class="text-4xl mb-3">🧠</div>
<div class="font-bold text-red-400">Cognitive Load</div>
<div class="text-sm text-gray-400 mt-2">Had to hold the entire Grid in your head to make any change</div>
</div>

<div class="text-center">
<div class="text-4xl mb-3">🚧</div>
<div class="font-bold text-red-400">Bottleneck</div>
<div class="text-sm text-gray-400 mt-2">Only 2 people "understood" it. Everyone else was afraid to touch it</div>
</div>

<div class="text-center">
<div class="text-4xl mb-3">🐌</div>
<div class="font-bold text-red-400">Slow Delivery</div>
<div class="text-sm text-gray-400 mt-2">Simple features took weeks. Bugs hid in the complexity</div>
</div>

</div>

<div v-click class="text-center mt-12 text-xl">
  <span class="text-gray-400">Technical debt</span> <span class="text-red-400 font-bold">compounds</span><span class="text-gray-400">. Every shortcut today is a tax on every change tomorrow.</span>
</div>

<!--
[2:30 - 3:30]

• Cognitive Load — hold 2000 lines in your head for any change

• Bottleneck — only 2 people "understood" it, rest afraid to touch

• Slow Delivery — simple features took weeks, bugs hid in complexity

• [click] Technical debt compounds — every shortcut is a tax on tomorrow
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
  // ... loading, error, etc.
  sortable = input(false);       // flag
  showHeader = input(false);     // flag
  persistState = input(false);   // flag + friends below

  storageKey = input<string>();  // only if persistState
  #storage = inject(StorageService);
  // ... more services
}
```

_"What features am I today?"_

<!--
[3:30 - 4:15]

• Grid too big for slides — built smaller example with same problems

• Think of YOUR codebase — that component with 15 inputs, every PR touches it

• Inputs for data... but also flags, storage keys, services used conditionally

• persistState doesn't come alone — brings storageKey, initialState, service

• Mega Component asks "what features am I today?" instead of just being a list

• Let's break it down
-->

---
layout: default
---

<template #title>

# The Journey

</template>

From Mega Component to clean architecture:

<v-clicks>

1. **Map** - The **WHERE** (Identify context)
2. **Extract** - The **WHAT** (Content Projection)
3. **Interface** - The **HOW** (Strategy via DI)
4. **Compose** - The **WHETHER** (Directives)
5. **Bundle** - The **WHICH** (hostDirectives)

</v-clicks>

<!--
[4:15 - 4:45]

• Here's the map — five steps from Mega Component to clean architecture

• [click] Map — what varies and where, shooting blind without it

• [click] Extract — pull behaviors out of the component

• [click] Interface — define contracts for swappable behaviors

• [click] Compose — make behaviors optional with directives

• [click] Bundle — group repeating patterns with hostDirectives

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
| showHeader | ✓ | ✗ | ✓ |
| sortable | ✓ | ✓ | ✗ |
| filterable | ✗ | ✓ | ✓ |
| persistState | ✗ | ✓ | ✗ |

**Baseline** = always there. **The rest** = opt-in.

<!--
[4:45 - 5:15]

• Map tool — simple matrix: rows = features, columns = contexts

• items everywhere? baseline — stays in component

• showHeader varies? signal for Content Projection

• persistState only in one place? doesn't belong in component

• Now we have a map — we know what to extract
-->

---
layout: section
---

# The Real Problem

Coupling isn't bad.

## Hidden coupling is.

<!--
[5:15 - 5:45]

• Coupling isn't bad — HIDDEN coupling is

• Visible deps (inputs, constructor) = manageable

• Buried in flags/templates = lose control, touch here, breaks there

• Question: not "remove coupling" but "make it VISIBLE"

• Inputs = your default, three extraction patterns when they fail
-->

---
layout: image-right
---

::left::

# Three Extraction Patterns

Inputs are your default.

**When they fail, reach for these.**

::default::

| The Tell            | Pattern            |
| ------------------- | ------------------ |
| Structural flags    | Content Projection |
| Behavioral bundles  | Strategy via DI    |
| Composable opt-ins  | Directives         |

<!--
[5:45 - 6:30]

• Three patterns — you know them, wisdom is knowing WHEN

• Inputs = visible coupling, stay there while it works

• Structural flags (booleans changing DOM) → Content Projection

• Behavioral bundles (flag + logic + services) → Strategy via DI

• Composable opt-ins (stackable behaviors) → Directives

• We'll learn to spot the tell
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
[6:30 - 7:15]

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
[7:15 - 7:45]

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
[7:45 - 8:15]

• Before: pass true, component renders internally

• After: no boolean, parent just... puts header there, or doesn't

• @if becomes ng-content — boolean evaporates

• Content projection = what happens when you stop making structural decisions inside

• Aha moment: projected content is just an @if you pulled out
-->

---
layout: section
---

<template #title>

# Tool 1: Content Projection

</template>

Separating the <span style="color: var(--cx-green); font-weight: bold;">WHAT</span>

What content appears. Not how it behaves.

<div class="text-sm text-gray-500 mt-8">
  <span class="text-yellow-500">Short term:</span> "Just one more @if"
  <span class="mx-4">→</span>
  <span class="text-red-400">Long term:</span> Template spaghetti, impossible to test
</div>

<!--
[8:15 - 8:45]

• Tool 1: Content Projection — separates the WHAT

• When inputs control STRUCTURE (what DOM exists)

• Short term: "just one more @if" → Long term: template spaghetti

• Like a picture frame — frame owns size/shape, you choose the picture

• Component owns layout, you own content
-->

---
layout: default
---

<template #title>

# Content Projection

</template>

```html [card.html]
<div class="header">
  <ng-content select="[header]" />
</div>
<ng-content />
```

Card owns layout. Consumer owns content via ng-content.

<!--
[8:45 - 9:15]

• Two slots: header + default

• "I'll handle the wrapper, you decide the content"

• Cards, Modals, Panels — wrapper fixed, content varies
-->

---
layout: default
---

<template #title>

# The Next Ceiling

</template>

Content Projection solved **WHAT** appears.

But what about **HOW** it behaves?

- _"Save to localStorage vs server"_
- _"Persist state when flag is true"_

<!--
[9:15 - 9:45]

• Good move — extracted structural decision, boolean gone, parent decides

• But success revealed a new ceiling

• Content Projection solves WHAT — what appears

• But what about HOW — how things are done?

• Ever tried ng-content for HttpClient? Doesn't work — ng-content is for DOM

• Need a tool that separates the HOW
-->

---
layout: default
---

<template #title>

# The Implementation Branch

</template>

```ts [list.ts]
save(state: ListState) {
  if (this.isProd()) {
    this.api.post('/preferences', state);
  } else {
    this.mockApi.save(state); // dev mode
  }
}
```

The component knows **too much** about the "how".

<!--
[9:45 - 10:00]

• if isProd → real API, else → Mock

• Component knows all options — knows too much

• What happens when you add staging? Testing? Another env?

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

How it's done. A or B, never both.

<div class="text-sm text-gray-500 mt-8">
  <span class="text-yellow-500">Short term:</span> "Just add an if for server mode"
  <span class="mx-4">→</span>
  <span class="text-red-400">Long term:</span> if-forests, untestable without mocks
</div>

<!--
[10:00 - 10:30]

• Tool 2: Strategy via DI — separates the HOW

• A or B, pick one, never both together

• Server or LocalStorage, Prod or Mock — only one runs

• Component doesn't know what it got — just asks "give me something that saves"

• Zero if-statements in component — decision happened before it was created

• Let's see code
-->

---
layout: image-right
---

::left::

# Strategy: The Problem

That **if-else** needs to disappear. But where does the decision go?

::default::

| Context     | Storage    |
| ----------- | ---------- |
| Production  | Real API   |
| Development | Mock API   |
| Testing     | In-memory  |

<!--
[10:30 - 10:45]

• if-else needs to disappear from component — but where to?

• Depends on context: Prod=real API, Dev=Mock, Testing=In-memory

• Same component, completely different behavior

• Before: more ifs, more booleans. Now: component doesn't care
-->

---
layout: default
---

<template #title>

# Strategy: The Interface

</template>

```ts [storage-strategy.ts]
export interface StorageStrategy {
  save(key: string, data: unknown): Promise<void>;
  load<T>(key: string): Promise<T | null>;
}

export const STORAGE_STRATEGY =
  new InjectionToken<StorageStrategy>('StorageStrategy');
```

<!--
[10:45 - 11:00]

• Interface: defines WHAT (save/load), not HOW

• InjectionToken = our key for DI

• (Promise for readability, Observable works same)

• Just the contract, no implementation yet
-->

---
layout: default
---

<template #title>

# Strategy: Two Implementations

</template>

<div class="grid grid-cols-2 gap-4">
<div>

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

</div>
<div>

```ts [server-storage.ts]
export class ServerStorageStrategy
  implements StorageStrategy
{
  #api = inject(ApiService);

  async save(key: string, data: unknown) {
    await firstValueFrom(this.#api.post(...));
  }
  async load<T>(key: string) {
    return firstValueFrom(this.#api.get(...));
  }
}
```

</div>
</div>

Same interface. Different "how".

<!--
[11:00 - 11:30]

• Two implementations: localStorage (left) and Server (right)

• localStorage — saves in browser, works offline

• Server — calls API, syncs across devices

• Same interface exactly — list calls save, doesn't know where it goes
-->

---
layout: default
---

<template #title>

# Strategy: The Provider

</template>

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
[11:30 - 12:15]

• Decision lives in PROVIDER

• AdminDashboard says: "below me, anyone asking for Storage gets ServerStorageStrategy"

• Angular's power: hierarchical injection — affects only subtree

• Zero if-statements in component

• Pro tip: NoopStrategy at root — nothing breaks by default, override where needed

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
  #storage = inject(STORAGE_STRATEGY);
  #sorter = inject(SORT_STRATEGY);
  #filter = inject(FILTER_STRATEGY);
  // ... and 5 more tokens
}
```

**Too many tokens.** Strategy solved **HOW**. But what about **WHETHER**?

<v-click>
<img src="/assets/one-does-not-simply.jpg" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-120 rounded-lg shadow-xl" />
</v-click>

<!--
[12:15 - 13:00]

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
layout: default
---

<template #title>

# The Hidden Behavior

</template>

```ts [list.ts]
persistState = input(false);
#storage = inject(STORAGE_STRATEGY); // always injected!

ngOnInit() {
  if (this.persistState()) {
    this.#storage.save(this.storageKey(), this.state());
  }
}
```

Service injected even when not needed. Invisible from outside.

<!--
[13:00 - 13:15]

• persistState flag exists, logic buried in ngOnInit

• Worse: STORAGE_STRATEGY injected ALWAYS, even when persistState=false

• From outside? Can't tell if list saves without reading code

• The tell: hidden behavior behind a flag = directive waiting to be born
-->

---
layout: section
---

<template #title>

# Tool 3: Directives

</template>

Separating the <span style="color: var(--cx-green); font-weight: bold;">WHETHER</span>

Is it on or off? Composable opt-ins.

<div class="text-sm text-gray-500 mt-8">
  <span class="text-yellow-500">Short term:</span> "Just inject it everywhere"
  <span class="mx-4">→</span>
  <span class="text-red-400">Long term:</span> Hidden features, null-check hell
</div>

<!--
[13:15 - 14:00]

• Tool 3: Directives — separates the WHETHER

• Short term: "inject everywhere". Long term: hidden features, null-check hell

• Recap: Content Projection = WHAT, Strategy = HOW

• But Strategy didn't solve WHETHER — list still injects STORAGE_STRATEGY always

• Directives fix this — injection moves to directive

• No directive? No injection

• No "maybe", no conditions — exists or doesn't — WHETHER

• Directive = small behavior chunk that brings its own dependencies
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
[14:00 - 14:30]

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
[14:30 - 15:00]

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

<v-click>
<img src="/assets/distracted-boyfriend.jpg" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-120 rounded-lg shadow-xl" />
</v-click>

<!--
[15:00 - 15:45]

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
[15:45 - 16:30]

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
[16:30 - 17:00]

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

<v-click>
<img src="/assets/pam-theyre-different.jpg" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-120 rounded-lg shadow-xl" />
</v-click>

<!--
[17:00 - 17:15]

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

```ts [auto-saveable.ts]
@Directive({
  selector: 'app-list[autoSaveable]',
  hostDirectives: [DirtyTrackable, Debounceable, Persistable],
})
export class AutoSaveable {
  #dirty = inject(DirtyTrackable);
  #debounce = inject(Debounceable);
  #persist = inject(Persistable);

  #autoSave = effect(() => {
    if (this.#dirty.isDirty()) {
      this.#debounce.run(() => this.#persist.save());
    }
  });
}
```

When A and B **must** work together.

<!--
[17:15 - 17:45]

• Last pattern: Coordinator

• Product asks: "auto-save, but only when dirty, with debounce"

• Have directive for Dirty, directive for Debounce — they don't know each other

• Coordinator connects them: "when dirty, run save through debounce"

• This IS coupling — but intentional. Give it a home, a name, write a test
-->

---
layout: center
---

<div class="text-center">
  <div class="text-gray-400 text-sm uppercase tracking-widest mb-6">The Journey</div>
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
[17:45 - 17:52]

• Five steps — journey complete
-->

---
layout: center
---

<div class="text-center">
  <div class="text-gray-400 text-sm uppercase tracking-widest mb-6">Team Superpowers</div>
  <div class="flex justify-center gap-8 mt-8">
    <div class="text-center">
      <div class="text-2xl mb-2">🎯</div>
      <div class="text-sm text-gray-300">Clear scope</div>
    </div>
    <div class="text-center">
      <div class="text-2xl mb-2">⚡</div>
      <div class="text-sm text-gray-300">Parallel work</div>
    </div>
    <div class="text-center">
      <div class="text-2xl mb-2">🧪</div>
      <div class="text-sm text-gray-300">Testable in isolation</div>
    </div>
    <div class="text-center">
      <div class="text-2xl mb-2">📍</div>
      <div class="text-sm text-gray-300">Single point of change</div>
    </div>
    <div class="text-center">
      <div class="text-2xl mb-2">💬</div>
      <div class="text-sm text-gray-300">Shared vocabulary</div>
    </div>
  </div>
</div>

<!--
[17:52 - 18:00]

• Each gave us a superpower:

• Clear scope — know exactly what to extract before starting

• Parallel work — one dev on list, another on header, no waiting

• Testable in isolation — each part testable separately

• Single point of change — add behavior? one place

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
[18:00 - 18:15]

• When NOT to use? Let's set boundaries
-->

---
layout: default
---

<template #title>

# Guardrails

</template>

| Pattern            | Don't use when...                         |
| ------------------ | ----------------------------------------- |
| Content Projection | You need behavior, not structure          |
| Strategy via DI    | Behaviors should be optional/composable   |
| Directives         | Same bundle repeated — name it instead    |
| hostDirectives     | Things are unrelated — don't bundle them  |

<!--
[18:15 - 18:30]

• Inputs = cheap. Extraction patterns = cost complexity

• Use only when price is already there

• Don't pull heavy artillery if simple input works

• Content Projection? Structure only. Not behavior

• Strategy? Not for optional things

• Directives? Don't copy-paste — name it

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
[18:30 - 19:00]

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
layout: image-right
---

::left::

# Decision Framework

Inputs are your default. When they fail:

**WHERE, WHAT, HOW, WHETHER, WHICH**

::default::

| The Tell           | Pattern            | Separates   |
| ------------------ | ------------------ | ----------- |
| Cross-context drift | Map Feature × Context | The **WHERE** (Map) |
| Structural flags   | Content Projection | The **WHAT**    |
| Behavioral bundles | Strategy via DI    | The **HOW**     |
| Composable opt-ins | Directives         | The **WHETHER** |
| Same combo 3x      | hostDirectives     | The **WHICH**    |

<!--
[19:00 - 19:45]

• Remember the journey? Map, Extract, Interface, Compose, Bundle

• Here's the summary — take a photo. Five questions: WHERE, WHAT, HOW, WHETHER, WHICH

• Map = WHERE — where is component used, what varies? Matrix

• Each tool answers different question:

• WHAT — what content? Content Projection

• HOW — how is it done? Strategy

• WHETHER — does it happen at all? Directives

• WHICH — which ones belong together? hostDirectives

• Inputs = home. Stay there

• When it breaks, look for the Tell:

• Component drifting across contexts? Matrix — Map

• Boolean changing structure? Content Projection — Extract

• Behavior bundle? Strategy — Interface

• Optional features? Directives — Compose

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
[19:45 - 20:45]

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
[20:45 - 21:45]

• Thank you all

• Got a component fighting you? Let's talk. I'm here

• Questions?
-->
