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

• "Hi friends, today I'm going to talk about how to avoid unnecessary pain by listening to your code"

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
[0:55 - 1:25] (30s)

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
[1:25 - 1:40] (15s)

• Before theory, or even practice — let's see what actually happened

• Built a Grid component — started simple: data, loading, options
-->

---
layout: section
---

# Then Requirements Came...

Custom templates, row styles, expand/collapse...

## Context menus, column management...

<!--
[1:40 - 2:00] (20s)

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
[2:00 - 2:25] (25s)

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
[2:25 - 2:40] (15s)

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
[2:40 - 2:55] (15s)

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
[2:55 - 3:10] (15s)

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
[3:10 - 3:25] (15s)

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
[3:25 - 3:40] (15s)

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
[3:40 - 3:55] (15s)

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
[3:55 - 4:05] (10s)

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
[4:05 - 4:15] (10s)

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
storageMode = input<'local' | 'session' | 'server'>('local');
#http = inject(HttpClient);
```

One flag → config + mode + service.

_"What features am I today?"_

<!--
[4:15 - 4:30] (15s)

• persistable brings storageKey, StorageService

• This component is having an identity crisis
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
[4:30 - 4:45] (15s)

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
[4:45 - 5:20] (35s)

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
[5:20 - 5:30] (10s)

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
[5:30 - 5:38] (8s)

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
[5:38 - 5:46] (8s)

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
[5:46 - 5:54] (8s)

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
[5:54 - 6:02] (8s)

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
[6:02 - 6:12] (10s)

• Bundle — group repeating patterns with hostDirectives

• Let's start with Map
-->

---
layout: default
---

<template #title>

# The Map (WHERE) - items

</template>

| Feature | Main Page | Admin Panel | Preview |
|---------|-----------|-------------|---------|
| items | ✓ | ✓ | ✓ |

**items** everywhere? That's the **baseline** — stays in component.

<!--
[6:12 - 6:25] (13s)

• Map tool — simple matrix: rows = features, columns = contexts

• items everywhere? baseline — stays in component
-->

---
layout: default
---

<template #title>

# The Map (WHERE) - showHeader

</template>

| Feature | Main Page | Admin Panel | Preview |
|---------|-----------|-------------|---------|
| showHeader | ✓ | ✗ | ✓ |

**showHeader** varies? Signal for **Content Projection**.

<!--
[6:25 - 6:35] (10s)

• showHeader varies? signal for Content Projection
-->

---
layout: default
---

<template #title>

# The Map (WHERE) - persistable

</template>

| Feature | Main Page | Admin Panel | Preview |
|---------|-----------|-------------|---------|
| persistable | Local | Server | Session |

**persistable** varies in **type** — not just on/off. Signal for **Strategy**.

<!--
[6:35 - 6:50] (15s)

• persistable varies — but not boolean! Local, Server, or Session storage

• Different implementations, not just toggle — needs Strategy pattern
-->

---
layout: default
---

<template #title>

# The Map (WHERE) - Directives

</template>

| Feature | Main Page | Admin Panel | Preview |
|---------|-----------|-------------|---------|
| sortable | ✓ | ✓ | ✗ |
| filterable | ✓ | ✗ | ✗ |

**Opt-in behaviors** — varies by context. Signal for **Directives**.

<!--
[6:50 - 7:00] (10s)

• sortable, filterable — optional, vary by context

• Each one is a candidate for Directives
-->

---
layout: default
---

<template #title>

# The Tell

</template>

```ts {1-4}
showHeader = input(false);
sortable = input(false);
filterable = input(false);
persistable = input(false);
```

Boolean flags. The component asking _"what features am I?"_

But look closer at **The Mega List**...

<!--
[7:00 - 7:35] (35s)

• showHeader, sortable, filterable, persistable — each is a feature toggle

• Component asking "what do you want me to be today?"

• The Tell — like poker, pile of booleans = component trying to be everything

• Notice: storageKey only relevant when persistable=true — they travel together

• But one of these flags is different — showHeader controls STRUCTURE, not behavior...
-->

---
layout: default
---

<template #title>

# The Structural Flag

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
[7:35 - 8:15] (40s)

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
[8:15 - 8:55] (40s)

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

# Content Projection

</template>

Separating the <span style="color: var(--cx-green); font-weight: bold;">WHAT</span>

What content appears. Not how it behaves.

<!--
[8:55 - 9:15] (20s)

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
[9:15 - 9:23] (8s)

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
[9:23 - 9:31] (8s)

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

- _"Save to localStorage vs server"_
- _"Sync across devices vs keep local"_

<!--
[9:31 - 10:05] (34s)

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

# The Implementation Branch

</template>

```ts
save(key: string, state: ListState) {
  if (this.storageMode() === 'local') {
    localStorage.setItem(key, JSON.stringify(state));
  } else if (this.storageMode() === 'session') {
    sessionStorage.setItem(key, JSON.stringify(state));
  } else if (this.storageMode() === 'server') {
    this.#http.post('/api/preferences', { key, state });
  }
}
```

The component knows **too much** about the "how".

<!--
[10:05 - 10:30] (25s)

• if local → localStorage, if session → sessionStorage, if server → HTTP call

• Component knows all storage implementations — knows too much

• What happens when you add IndexedDB? Cloud sync?

• More else-if? And another?

• The tell: if-else on implementations = Strategy
-->

---
layout: image-right
---

::left::

# Strategy: The Problem

That **if-else** needs to disappear. But where does the decision go?

::default::

| Context      | Storage   |
| ------------ | --------- |
| Main Page    | Local     |
| Admin Panel  | Server    |
| Preview      | Session   |

<!--
[11:06 - 11:25] (19s)

• if-else needs to disappear from component — but where to?

• Depends on context: Main=local, Admin=server, Preview=session

• Same component, completely different storage behavior

• Before: more ifs, more booleans. Now: component doesn't care
-->

---
layout: default
---

<template #title>

# Strategy: The Interface

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
[11:25 - 11:45] (20s)

• Interface: defines WHAT (save/load), not HOW

• InjectionToken = our key for DI

• Takes key and state, returns nothing or loaded state

• Just the contract, no implementation yet
-->

---
layout: default
---

<template #title>

# Strategy: LocalStorage

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
[11:45 - 12:00] (15s)

• LocalStorage — persists even after browser closes

• Simple, no server needed, works offline
-->

---
layout: default
---

<template #title>

# Strategy: ServerStorage

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
[12:00 - 12:20] (20s)

• ServerStorage — syncs to backend, available on any device

• Same interface exactly — directive calls save/load, doesn't know which strategy
-->

---
layout: default
---

<template #title>

# Strategy: The Provider

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
[12:20 - 13:05] (45s)

• Decision lives in PROVIDER

• AdminPanel says: "below me, anyone asking for Storage gets ServerStorage"

• Angular's power: hierarchical injection — affects only subtree

• Zero if-statements in component

• Pro tip: LocalStorage at root — works by default, override where needed

• Strategy = exclusive choice. A or B, never both.

• This is VISIBLE coupling
-->

---
layout: section
---

<template #title>

# Strategy via DI

</template>

Separating the <span style="color: var(--cx-green); font-weight: bold;">HOW</span>

How it's done. Local or Server, never both.

<!--
[13:05 - 13:25] (20s)

• Tool 2: Strategy Pattern via DI

• Separates the HOW — not if, but WHICH implementation

• When behavior varies by context, strategy prevents if-cascades

• Classic pattern from Gang of Four — Angular makes it elegant via DI
-->

---
layout: section
---

# The Shortcut

"Just add an if for each storage type"

<!--
[13:25 - 13:33] (8s)

• The tempting path — if local do this, if server do that

• We all know where this leads
-->

---
layout: section
---

# The Tax

if-forests.

## Untestable without mocks.

<!--
[13:33 - 13:41] (8s)

• if-else branches multiply — add cloud? another branch everywhere

• Testing requires mocking everything to hit each branch

• Can't reason about code without tracing all paths
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
[13:41 - 14:26] (45s)

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
layout: default
---

<template #title>

# Directive: Persistable

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
[15:02 - 15:41] (39s)

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

# Directives: Usage

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
[15:41 - 16:21] (40s)

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

# The Sign

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

<img v-click src="/assets/distracted-boyfriend.jpg" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-120 rounded-lg shadow-xl" />

<!--
[16:21 - 17:01] (40s)

• Three different pages, exact same combo — copy-paste

• Coupling hides inside repetition: "we always put these three together"

• Want to add a fourth? Hunt through entire app. Miss one? Drift begins

• [click] Distracted by repetition

• Same thing 3 times? Not coincidence — concept without a name yet
-->

---
layout: section
---

<template #title>

# The Promotion Rule

</template>

One time is just code.

Two times is coincidence.

**Three times is a concept.**

## Name it.

<!--
[17:01 - 17:41] (40s)

• Once = code. Twice = coincidence. Three times = concept — NAME IT

• Caveat: must be same REASON. Different reasons? Don't combine — false duplication

• With a name: becomes entity, can document, test, discuss in daily

• Without name: things drift. Saw 3 "identical" lists, one had sorting off "temporarily", 6 months later nobody remembered why

• Name it
-->

---
layout: section
---

<template #title>

# Directives

</template>

Separating the <span style="color: var(--cx-green); font-weight: bold;">WHETHER</span>

Is it on or off? Composable opt-ins.

<!--
[17:41 - 18:01] (20s)

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
[18:01 - 18:09] (8s)

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
[18:09 - 18:17] (8s)

• Reality: can't tell what's active without reading code

• Every service needs null checks and guards
-->

---
layout: default
---

<template #title>

# hostDirectives

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
[18:17 - 18:57] (40s)

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

# Before/After

</template>

```html
<!-- Before: 3 directives -->
<app-list appListSortable appListFilterable appListPersistable [items]="data" />

<!-- After: 1 named concept -->
<app-list appListPowerList [items]="data" />
```

<img v-click src="/assets/drake-coupling.jpg" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-120 rounded-lg shadow-xl" />

<!--
[18:57 - 19:17] (20s)

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

# Coordinator Directive

</template>

```ts
@Directive({
  selector: 'app-list[appListPersistSelection]',
  hostDirectives: [AppListSelectable, AppListPersistable],
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
[19:17 - 19:52] (35s)

• Last pattern: Coordinator — the glue

• Product asks: "remember which items user selected"

• Two independent directives: Selectable tracks selection, Persistable saves state

• Neither knows about the other — Coordinator connects them

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
[19:52 - 19:59] (7s)

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
[19:59 - 20:07] (8s)

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
[20:07 - 20:15] (8s)

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
[20:15 - 20:23] (8s)

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
[20:23 - 20:31] (8s)

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
[20:31 - 20:42] (11s)

• Shared vocabulary — say "PowerList" in daily, everyone knows

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
[20:42 - 20:52] (10s)

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
[20:52 - 21:02] (10s)

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
[21:02 - 21:12] (10s)

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
[21:12 - 21:22] (10s)

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
[21:22 - 21:35] (13s)

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
[21:35 - 22:17] (42s)

• Remember the price we paid?

• Cognitive Load

• Bottleneck 

• Slow Delivery

• My metric: how many places to touch for small change. Went from "many" to "one"

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
[22:17 - 22:29] (12s)

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
[22:29 - 22:39] (10s)

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
[22:39 - 22:49] (10s)

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
[22:49 - 22:59] (10s)

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
[22:59 - 23:09] (10s)

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
[23:09 - 23:22] (13s)

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
[23:22 - 24:32] (70s)

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
[24:32 - 24:47] (15s)

• Thank you all

• Got a component fighting you? Let's talk. I'm here

• Questions?
-->
