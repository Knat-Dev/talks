---
theme: ./theme
title: Decoupling in Angular
info: |
  ## Decoupling in Angular
  Letting the Code Tell You What It Needs

  Dor Peled · @Knat-Dev

  Runtime: ~24-25 min content + 5 min Q&A = ~30 min total
layout: a-main-cover-2
highlighter: shiki
mdc: true
---

# Decoupling in Angular

## Letting the Code Tell You What It Needs

<div class="absolute bottom-[30px] left-[80px] flex items-center gap-3">
  <img src="/assets/profile.jpg" class="w-24 h-24 rounded-full object-cover border-2 border-white/50" />
  <div class="text-white/80 text-2xl flex items-center gap-1">Dor Peled <span class="opacity-50">·</span> <span class="text-xl opacity-90">@Knat-Dev</span></div>
</div>

<!--
[0:00 - 0:30] (30s)

• "Hi friends, today I'm going to talk about how to avoid unnecessary pain by listening to your code"

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
    <div v-click class="flex items-center gap-3">
      <span class="text-2xl">🎸</span>
      <span class="text-gray-600">
        Playing guitar ~20 years (metal bands in the past)
      </span>
    </div>
    <div v-click class="flex items-center gap-3">
      <span class="text-2xl">🤘</span>
      <span class="text-gray-600">Metal — taste, not background noise</span>
    </div>
    <div v-click class="flex items-center gap-3">
      <span class="text-2xl">📚</span>
      <span class="text-gray-600">Fantasy & long-form worlds</span>
    </div>
    <div v-click class="flex items-center gap-3">
      <span class="text-2xl">🖥️</span>
      <span class="text-gray-600">Homelab tinkering</span>
    </div>
  </div>
</div>

<!--
[0:30 - 1:05] (35s)

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
[1:05 - 1:40] (35s)

• Hands up: who added "just one more input"?

• Hands up: who waited for someone to finish editing the same file?

• Let's see the component that taught me this lesson
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
[1:40 - 1:58] (18s)

• Before theory, or even practice — let's see what actually happened

• Built a Grid component — started simple: data, loading, options
-->

---
layout: section
---

# Then Requirements Came...

<ul class="mt-6">
  <li>Custom templates</li>
  <li v-click>Row styles</li>
  <li v-click>Expand/collapse</li>
  <li v-click>Context menus</li>
  <li v-click>Column management</li>
</ul>

<!--
[1:58 - 2:22] (24s)

• Requirements started coming in...

• Custom templates, row styles, expand/collapse, context menus

• Column management, global state deps...

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

<img v-click src="/assets/this-is-fine.jpg" class="absolute bottom-8 right-8 w-80 rounded-lg shadow-xl" />

<!--
[2:22 - 2:52] (30s)

• The big one — every team wants to use it, but they all have slightly different needs

• [click] Who maintained something like this? Let me tell you what happened
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
[2:52 - 3:10] (18s)

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
[3:10 - 3:28] (18s)

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
[3:28 - 3:46] (18s)

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
[3:46 - 4:04] (18s)

• Technical debt compounds — every shortcut is a tax on tomorrow
-->

---
layout: default
---

<template #title>

# The Mega List

</template>

```ts
export class ListComponent<T> {
  items = input<T[]>([]);

  showHeader = input(false);
  sortable = input(false);
  filterable = input(false);
  persistable = input(false);
}
```

Four boolean flags. Looks innocent.

<!--
[4:04 - 4:22] (18s)

• Grid too big for slides — built smaller example with same problems

• Four boolean flags — looks innocent, right?

• But booleans never come alone...
-->

---
layout: default
---

<template #title>

# Booleans Bring Friends: showHeader

</template>

```ts
showHeader = input(false);
headerTitle = input('');
headerActions = input<Action[]>([]);
```

One flag → three inputs.

<!--
[4:22 - 4:40] (18s)

• showHeader brings headerTitle, headerActions

• One boolean → three inputs to configure
-->

---
layout: default
---

<template #title>

# Booleans Bring Friends: sortable

</template>

```ts
sortable = input(false);
sortKey = input<string>('');
sortDir = input<'asc' | 'desc'>('asc');
```

One flag → two config inputs.

<!--
[4:40 - 4:52] (12s)

• sortable brings sortKey, sortDir

• Now we need to know which column and direction
-->

---
layout: default
---

<template #title>

# Booleans Bring Friends: filterable

</template>

```ts
filterable = input(false);
filterKey = input<string>('');
filterValue = input<string>('');
```

One flag → two config inputs.

<!--
[4:52 - 5:04] (12s)

• filterable brings filterKey, filterValue

• More inputs to configure
-->

---
layout: default
---

<template #title>

# Booleans Bring Friends: persistable

</template>

```ts
persistable = input(false);
storageKey = input<string>('');
storageMode = input<'local' | 'server'>('local');
#http = inject(HttpClient);
```

One flag → config + mode + service.

_"What features am I today?"_

<!--
[5:04 - 5:22] (18s)

• persistable brings storageKey, StorageService

• This component is having an identity crisis
-->

---
layout: default
---

<template #title>

# God Component

</template>

> A component that knows too much or does too much. It violates the **single responsibility principle**.

<div v-click class="text-gray-600 mt-1">

> **SRP**: Does one thing and does it well.

</div>

Our **Mega List** is becoming one.

<!--
[5:22 - 5:40] (18s)

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
[5:40 - 6:22] (42s)

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
[6:22 - 6:34] (12s)

• Here's the journey — five questions from God Component to clean architecture
-->

---
layout: center
---

<div class="text-center">
  <div class="text-6xl font-bold mb-4"><span style="color: var(--cx-green);">1. Where</span></div>
  <div class="text-2xl text-gray-600">Map the variation points</div>
</div>

<!--
[6:34 - 6:44] (10s)

• 1. Where — what varies and where, shooting blind without it
-->

---
layout: center
---

<div class="text-center">
  <div class="text-6xl font-bold mb-4"><span style="color: var(--cx-green);">2. What</span></div>
  <div class="text-2xl text-gray-600">Extract structural decisions</div>
</div>

<!--
[6:44 - 6:54] (10s)

• 2. What — pull behaviors out of the component
-->

---
layout: center
---

<div class="text-center">
  <div class="text-6xl font-bold mb-4"><span style="color: var(--cx-green);">3. How</span></div>
  <div class="text-2xl text-gray-600">Define contracts for swappable behaviors</div>
</div>

<!--
[6:54 - 7:04] (10s)

• 3. How — define contracts for swappable behaviors
-->

---
layout: center
---

<div class="text-center">
  <div class="text-6xl font-bold mb-4"><span style="color: var(--cx-green);">4. Whether</span></div>
  <div class="text-2xl text-gray-600">Make behaviors optional</div>
</div>

<!--
[7:04 - 7:14] (10s)

• 4. Whether — make behaviors optional with directives
-->

---
layout: center
---

<div class="text-center">
  <div class="text-6xl font-bold mb-4"><span style="color: var(--cx-green);">5. Which</span></div>
  <div class="text-2xl text-gray-600">Bundle repeating patterns</div>
</div>

<!--
[7:14 - 7:26] (12s)

• 5. Which — group repeating patterns with hostDirectives

• Let's start with 1. Where
-->

---
layout: default
---

<template #title>

# 1. Where

</template>

<div class="flex gap-6 justify-center">
  <div class="border-2 rounded-lg p-5" style="border-color: var(--cx-green);">
    <div class="text-xl font-bold mb-4 text-center">Main Page</div>
    <div class="text-green-500">✓ items</div>
    <div class="text-green-500">✓ showHeader</div>
  </div>
  <div v-click class="border-2 rounded-lg p-5" style="border-color: var(--cx-green);">
    <div class="text-xl font-bold mb-4 text-center">Admin Panel</div>
    <div class="text-green-500">✓ items</div>
    <div class="text-red-400">✗ showHeader</div>
  </div>
</div>

<div class="mt-8 text-center">

**items** everywhere? **Baseline.** &nbsp;&nbsp;|&nbsp;&nbsp; **showHeader** differs? **Candidate for extraction.**

</div>

<!--
[7:26 - 8:00] (34s)

• Simple matrix: rows = features, columns = contexts

• items everywhere? baseline — stays in component

• showHeader varies? candidate for extraction — we'll tackle this first
-->

---
layout: default
---

<template #title>

# 2. What

</template>

```ts
// From The Mega List:
showHeader = input(false);
headerTitle = input('');
headerActions = input<Action[]>([]);
```

```html
@if (showHeader()) {
  <app-header [title]="headerTitle()" [actions]="headerActions()" />
}
<div class="list-body">...</div>
```

Why does the **list** decide whether a header exists?

<!--
[8:00 - 8:48] (48s)

• showHeader is different — controls STRUCTURE, not behavior

• @if in template decides if Header renders

• Next request: "show icon only on Thursdays", "header only for US"...

• How many inputs? How many @ifs until it breaks?

• Component asks "how should I look?" — not its job

• Why should the list decide if it has a header?
-->

---
layout: section
---

# The Shortcut

"Just one more @if"

<!--
[8:48 - 9:03] (15s)

• The familiar excuse — just add one more conditional

• Seems harmless in the moment
-->

---
layout: section
---

# The Tax

Template spaghetti.

## <span class="text-red-400">Impossible to test.</span>

<!--
[9:03 - 9:21] (18s)

• Reality: template becomes unreadable

• Testing requires mocking every branch

• There's a better way...
-->

---
layout: default
---

<template #title>

# 2. What

</template>

```html
<ng-content />
<div class="list-body">...</div>
```

Three inputs gone. **Just a slot.**

<!--
[9:21 - 9:45] (24s)

• ng-content with selector — a slot

• Header exists? It renders. Doesn't exist? Nothing there

• Component doesn't care — parent decides
-->

---
layout: default
---

<template #title>

# 2. What

</template>

```html
<!-- Before: component decides structure -->
<app-list [showHeader]="true" headerTitle="Items" [headerActions]="actions" />
```

```html
<!-- After: parent decides structure -->
<app-list>
  <app-list-header title="Items" [actions]="actions" />
</app-list>
```

The **@if** becomes a **slot**. The boolean **and its friends** disappear.

<!--
[9:45 - 10:09] (24s)

• Before: pass true, component renders internally

• After: no boolean, parent just... puts header there, or doesn't

• @if becomes ng-content — boolean evaporates
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
- _"Sync across devices vs keep local"_

<!--
[10:19 - 11:00] (41s)

• Good move — extracted structural decision, boolean gone, parent decides

• But success revealed a new ceiling

• Content Projection solves WHAT — what appears

• But what about HOW — how things are done?

• Storage logic: local vs server — completely different implementations

• Need a tool that separates the HOW
-->

---
layout: default
---

<template #title>

# 3. How

</template>

```ts
save(key: string, state: ListState) {
  if (this.storageMode() === 'local') {
    localStorage.setItem(key, JSON.stringify(state));
  } else if (this.storageMode() === 'server') {
    this.#http.post('/api/preferences', { key, state });
  }
}
```

The component knows **too much** about the "how".

<!--
[11:00 - 11:30] (30s)

• if local → localStorage, if server → HTTP call

• Component knows all storage implementations — knows too much

• What happens when you add IndexedDB? Cloud sync?

• More else-if? And another?

• The sign: if-else on implementations = Strategy
-->

---
layout: section
---

# The Shortcut

"Just add an if for each storage type"

<!--
[11:30 - 11:45] (15s)

• The tempting path — if local do this, if server do that

• We all know where this leads
-->

---
layout: section
---

# The Tax

if-forests.

## <span class="text-red-400">Untestable without mocks.</span>

<!--
[11:45 - 12:03] (18s)

• if-else branches multiply — add cloud? another branch everywhere

• Testing requires mocking everything to hit each branch

• There's a better way...
-->

---
layout: default
---

<template #title>

# 3. How

</template>

That **if-else** needs to disappear. But where does the decision go?

<div class="flex gap-6 justify-center mt-8">
  <div class="border-2 rounded-lg p-5" style="border-color: var(--cx-green);">
    <div class="text-xl font-bold mb-2 text-center">Main Page</div>
    <div class="text-center text-gray-600">Local</div>
  </div>
  <div v-click class="border-2 rounded-lg p-5" style="border-color: var(--cx-green);">
    <div class="text-xl font-bold mb-2 text-center">Admin Panel</div>
    <div class="text-center text-gray-600">Server</div>
  </div>
</div>

<!--
[12:03 - 12:26] (23s)

• if-else needs to disappear from component — but where to?

• Depends on context: Main=local, Admin=server

• Same component, completely different storage behavior

• Before: more ifs, more booleans. Now: component doesn't care
-->

---
layout: default
---

<template #title>

# 3. How

</template>

```ts
export interface StorageStrategy {
  save(key: string, state: ListState): void;
  load(key: string): ListState | null;
}

export const STORAGE_STRATEGY =
  new InjectionToken<StorageStrategy>('StorageStrategy');
```

<!--
[12:26 - 12:50] (24s)

• Interface defines WHAT (save/load), not HOW

• InjectionToken = our key for DI

• Takes key and state, returns nothing or loaded state

• Just the contract, no implementation yet
-->

---
layout: default
---

<template #title>

# 3. How

</template>

```ts
export class LocalStorage implements StorageStrategy {
  save(key: string, state: ListState) {
    localStorage.setItem(key, JSON.stringify(state));
  }
  load(key: string) {
    return JSON.parse(localStorage.getItem(key) ?? 'null');
  }
}
```

Persists across browser sessions. Works offline.

<!--
[12:50 - 13:08] (18s)

• LocalStorage — persists even after browser closes

• Simple, no server needed, works offline
-->

---
layout: default
---

<template #title>

# 3. How

</template>

```ts
export class ServerStorage implements StorageStrategy {
  #http = inject(HttpClient);

  save(key: string, state: ListState) {
    this.#http.post('/api/preferences', { key, state });
  }
  load(key: string) {
    return this.#http.get<ListState>(`/api/preferences/${key}`);
  }
}
```

Syncs across devices. Requires authentication.

**Same interface. Different "how".**

<!--
[13:08 - 13:32] (24s)

• ServerStorage — syncs to backend, available on any device

• Same interface exactly — directive calls save/load, doesn't know which strategy
-->

---
layout: default
---

<template #title>

# 3. How

</template>

```ts
@Component({
  providers: [
    {
      provide: STORAGE_STRATEGY,
      useClass: ServerStorage,
    },
  ],
})
export class AdminPanel {}
```

**Zero if-statements.** Context decides, not component.

<!--
[13:32 - 14:26] (54s)

• Decision lives in PROVIDER

• AdminPanel says: "below me, anyone asking for Storage gets ServerStorage"

• Angular's power: hierarchical injection — affects only subtree

• Zero if-statements in component

• Pro tip: LocalStorage at root — works by default, override where needed

• Strategy = exclusive choice. A or B, never both.

• This is VISIBLE coupling
-->

---
layout: default
---

<template #title>

# The Next Ceiling

</template>

```ts
export class ListComponent {
  #storage = inject(STORAGE_STRATEGY);
  #sorter = inject(SORT_STRATEGY);
  #filter = inject(FILTER_STRATEGY);
  // ... and 5 more tokens
}
```

**Too many tokens.** Strategy solved **HOW**. But what about **WHETHER**?

<img v-click src="/assets/one-does-not-simply.jpg" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-120 rounded-lg shadow-xl" />

<!--
[14:36 - 15:30] (54s)

• Strategy worked — HOW is out, zero ifs

• But success revealed something new

• 3 tokens here, 5 more off-slide — component injects ALL, always, even when not needed

• What if sorting is optional? Storage not relevant?

• [click] One does not simply inject everything

• Main problem: can't see in template what's on

• New dev opens file — no clue what this list does without reading code

• Strategy solved HOW — need tool for WHETHER, and it must be VISIBLE
-->

---
layout: section
---

# The Shortcut

"Just inject it everywhere"

<!--
[15:30 - 15:45] (15s)

• The familiar excuse — inject all services, check flags later

• Easier than thinking about what's actually needed
-->

---
layout: section
---

# The Tax

Hidden features.

## <span class="text-red-400">Null-check hell.</span>

<!--
[15:45 - 16:03] (18s)

• Reality: can't tell what's active without reading code

• Every service needs null checks and guards

• There's a better way...
-->

---
layout: default
---

<template #title>

# 4. Whether

</template>

```ts
@Directive({ selector: 'app-list[appListPersistable]' })
export class AppListPersistable {
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
[16:03 - 16:50] (47s)

• Selector: `app-list[appListPersistable]` — no attribute = doesn't exist, zero overhead

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

# 4. Whether

</template>

```html
<!-- Simple -->
<app-list [items]="data" />

<!-- With persistence -->
<app-list appListPersistable storageKey="admin-list" [items]="data" />

<!-- Full-featured -->
<app-list appListSortable sortKey="date" sortDir="desc"
          appListFilterable appListPersistable storageKey="main" [items]="data" />
```

**Visible in the template.** Look at the HTML, know what it does.

<!--
[16:50 - 17:38] (48s)

• Simple list? Nothing. Zero unnecessary injections

• Want persistence? Add `appListPersistable` — directive brings storageKey

• STORAGE_STRATEGY injected ONLY when appListPersistable exists

• Simple list? No storage service. No null checks

• Everything visible — new dev sees EXACTLY what this list does

• Each directive independent — combine in any order

• This is the visibility payoff
-->

---
layout: default
---

<template #title>

# 4. Whether

</template>

```html
<!-- Page A -->
<app-list appListSortable appListFilterable appListPersistable [items]="a" />

<!-- Page B -->
<app-list appListSortable appListFilterable appListPersistable [items]="b" />

<!-- Page C -->
<app-list appListSortable appListFilterable appListPersistable [items]="c" />
```

Same combo. Three times.

<!--
[17:38 - 18:26] (48s)

• Three different pages, exact same combo — copy-paste

• Coupling hides inside repetition: "we always put these together"

• Want to add a fourth? Hunt through entire app. Miss one? Drift begins

• Same thing three times? That's a concept without a name
-->


---
layout: section
---

<template #title>

# 5. Which

</template>

One time is just code.

Two times is coincidence.

**Three times is a concept.**

## Name it.

<!--
[18:36 - 19:24] (48s)

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

# 5. Which

</template>

```ts
@Directive({
  selector: 'app-list[appListPowerList]',
  hostDirectives: [
    { directive: AppListSortable, inputs: ['sortKey', 'sortDir'] },
    { directive: AppListFilterable, inputs: ['filterKey'] },
    AppListPersistable,
  ],
})
export class AppListPowerList {}
```

Forward inputs explicitly. **No magic.**

<!--
[19:24 - 20:12] (48s)

• Angular has `hostDirectives`

• AppListPowerList bundles all three — one directive brings the whole package

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

# 5. Which

</template>

```html
<!-- Before: 3 directives -->
<app-list appListSortable appListFilterable appListPersistable [items]="data" />

<!-- After: 1 named concept -->
<app-list appListPowerList [items]="data" />
```

<img v-click src="/assets/drake-coupling.jpg" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-120 rounded-lg shadow-xl" />

<!--
[20:12 - 20:36] (24s)

• Before: grocery list of directives. After: `appListPowerList`

• [click] Drake approves

• Code runs same thing. In your head? Different world

• New dev joins, sees `appListPowerList` — understands immediately

• Turned a list of things into a concept with meaning
-->

---
layout: default
---

<template #title>

# 5. Which

</template>

```ts
@Directive({
  selector: 'app-list[appListSelectable][appListPersistable]',
})
export class AppListPersistSelection {
  #selectable = inject(AppListSelectable);
  #persistable = inject(AppListPersistable);

  constructor() {
    // The Glue: selection changes → persist
    effect(() => this.#persistable.save(this.#selectable.selection()));
  }
}
```

The **Glue**. Selectable doesn't know Persistable. This connects them.

<!--
[20:36 - 21:18] (42s)

• Last pattern: Coordinator — the glue

• Product asks: "remember which items user selected"

• Two independent directives: Selectable tracks selection, Persistable saves state

• Neither knows about the other — Coordinator connects them

• This IS coupling — but intentional. Give it a home, a name, write a test
-->

<!--
[21:18 - 21:28] (10s)

• hostDirectives? Only bundle related things
-->

---
layout: center
---

<div class="text-center">
  <div class="text-gray-600 text-sm uppercase tracking-widest mb-6">The Journey</div>
  <div class="flex justify-center gap-4">
    <div class="px-4 py-2 rounded-full text-sm font-semibold shadow-lg" style="background: var(--cx-green); color: #111827;">
      ✓ 1. Where
    </div>
    <div class="px-4 py-2 rounded-full text-sm font-semibold shadow-lg" style="background: var(--cx-green); color: #111827;">
      ✓ 2. What
    </div>
    <div class="px-4 py-2 rounded-full text-sm font-semibold shadow-lg" style="background: var(--cx-green); color: #111827;">
      ✓ 3. How
    </div>
    <div class="px-4 py-2 rounded-full text-sm font-semibold shadow-lg" style="background: var(--cx-green); color: #111827;">
      ✓ 4. Whether
    </div>
    <div class="px-4 py-2 rounded-full text-sm font-semibold shadow-lg" style="background: var(--cx-green); color: #111827;">
      ✓ 5. Which
    </div>
  </div>
</div>

<!--
[21:28 - 21:36] (8s)

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
[21:36 - 21:46] (10s)

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
[21:46 - 21:56] (10s)

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
[21:56 - 22:06] (10s)

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
[22:06 - 22:16] (10s)

• Single point of change — add behavior? one place
-->

---
layout: center
---

<div class="text-center">
  <div class="text-8xl mb-8">💬</div>
  <div class="text-4xl font-bold mb-4">Shared Vocabulary</div>
  <div class="text-xl text-gray-600">Say "PowerList" in daily — everyone knows</div>
</div>

<!--
[22:16 - 22:29] (13s)

• Shared vocabulary — say "PowerList" in daily, everyone knows
-->

---
layout: default
---

<template #title>

# Decision Framework

</template>

| Separates | The Sign | Tool |
|-----------|----------|------|
| **1. Where** | Cross-context drift | Feature × Context |
| **2. What** | Structural flags | Content Projection |
| **3. How** | Exclusive alternatives | Strategy via DI |
| **4. Whether** | Composable opt-ins | Directives |
| **5. Which** | Same combo 3× | hostDirectives |

<div class="mt-8 text-center text-xl text-gray-600">
Code talks to you. Your job: <strong>listen</strong>, pick the right tool.
</div>

<!--
[22:29 - 23:05] (36s)

• Component drifting across contexts? Matrix — 1. Where

• Boolean changing structure? Content Projection — 2. What

• Exclusive alternatives? Strategy — 3. How

• Optional features? Directives — 4. Whether

• Copy-paste 3x? hostDirectives — 5. Which

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

<!--
[23:05 - 24:29] (84s)

• "Good abstractions aren't chosen. They're discovered."

• Remember the Grid from the start? 2000 lines. 2 people understood. Everyone afraid to touch

• Now? Same Grid. But now it has clear boundaries. Each feature in its place. Any dev can touch their part

• Didn't happen because we read Design Patterns book

• Happened because we listened to code, spotted the signs, extracted in time

• My challenge to you:

• Tomorrow morning, find one component

• Look at one boolean flag

• Ask: "who should really own this?"

• Don't change the code. Just think about it

• That's where discovery begins
-->

---
layout: a-main-cover-2
---

# Thank You

<template #right>
  <img src="/assets/qr-slides.png" class="w-48 rounded-lg shadow-lg border-4 border-white" />
  <div class="text-center mt-4">
    <div class="text-xl font-bold text-white">
    <a href="https://slides.knat.dev" target="_blank">slides.knat.dev</a>
    </div>
  </div>
</template>


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
[24:29 - 24:47] (18s)

• Thank you all

• Got a component fighting you? Let's talk. I'm here

• Questions?
-->
