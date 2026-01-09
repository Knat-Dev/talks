---
theme: ./theme
title: Decoupling in Angular
info: |
  ## Decoupling in Angular
  Letting the Code Tell You What It Needs

  Dor Peled · @Knat-Dev

  Runtime: ~2,900 words in speaker notes.
  150 wpm (natural pace) = ~19 min.
  120 wpm (with pauses) = ~24 min.
layout: a-main-cover-2
highlighter: shiki
transition: slide-left
mdc: true
---

# Decoupling in Angular

## Letting the Code Tell You What It Needs

<div class="flex items-center gap-6 mt-4">
  <img src="/assets/profile.jpg" class="w-24 h-24 rounded-full object-cover" alt="Dor Peled" />
  <div>
    <div class="font-bold text-xl">Dor Peled</div>
    <div class="opacity-80">Software Engineer @ Coralogix</div>
    <div class="flex gap-4 mt-3 text-lg">
      <a href="https://github.com/Knat-Dev" target="_blank" class="opacity-75 hover:opacity-100"><carbon-logo-github /></a>
      <a href="https://www.linkedin.com/in/dor-peled-swe/" target="_blank" class="opacity-75 hover:opacity-100"><carbon-logo-linkedin /></a>
    </div>
  </div>
</div>

<!--
BEATS:<br>• Intro — Dor, Coralogix<br>• Raise hands: who's added "just one more input"?<br>• That's the start of a God Component<br>• Hunting "tells" — like poker<br>• Docs = syntax, Pain = architecture<br>• Set up the grid story

[0:00 - 1:15]

Hey everyone. I'm Dor — software engineer at Coralogix.

Quick show of hands — who's added "just one more input" to a component? One more boolean flag?

[Wait for hands]

Yeah. Me too. That's how God Components start. Not with bad intentions — with reasonable feature requests.

Today we're hunting tells. Like poker tells. That moment you look at code and think "something's wrong here" but can't explain what.

Docs teach you syntax. Pain teaches you architecture. I'm gonna show you the pain — so you recognize it before it bites.

Let me tell you about a component that taught me everything I know about decoupling.

[Scan the room — make eye contact]
-->

---
layout: center
---

<div class="flex flex-col items-center gap-6">
  <img src="/assets/qr-slides.png" class="w-48 rounded-lg shadow-lg" />
  <div class="text-center">
    <div class="text-2xl font-bold">slides.knat.dev</div>
    <div class="text-gray-400 text-sm mt-2">Follow along on your device</div>
  </div>
</div>

<!--
BEATS:<br>• QR code — follow along

Quick — scan this if you wanna follow along on your phone or laptop.
-->

---
layout: default
---

# The Grid

At work, I built a grid wrapper. It started simple.

Then it didn't.

<v-click>
<img src="/assets/this-is-fine.jpg" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-120 rounded-lg shadow-xl" />
</v-click>

<!--
BEATS:<br>• Real problem — grid wrapper at work<br>• Started simple, features piled up<br>• Context menu forced coupling<br>• Drawer pain, twice<br>• [CLICK MEME] Show of hands<br>• I'll show you how to fix it

[1:30 - 3:00]

Before I teach you anything, let me show you the real problem.

At work, I built a grid component. Day one: client-side fetching, row data, done.

Then features piled up. Cell renderers. Row styles. Expand/collapse. Context menu — and here's where it got bad. The context menu forced coupling. Every consumer had to provide the same things.

We used it in the main page. Fine. Then we added it to a drawer. Messy. Coupled to global state, services everywhere.

Then we added it to ANOTHER drawer. Same pain.

[Click for meme]

Show of hands — who's worked on a component like this?

[Pause for hands]

Yeah. This is real. And I'm gonna show you how we fixed it. But first, let me teach you the tools.
-->

---
layout: section
---

# The Laboratory

A <span class="text-emerald-400 font-mono">ListComponent</span> built to fail.

So we can dissect it.

<!--
BEATS:<br>• Can't fit grid on a slide<br>• Built a ListComponent with same crimes<br>• Wrote it bad on purpose

[3:00 - 3:30]

The Grid has too much domain stuff — can't fit it on a slide.

So I built a ListComponent that commits the exact same architectural crimes. I wrote it bad on purpose.

Let's dissect it.
-->

---
layout: default
---

# The God List

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

_"What features am I today?"_

<!--
BEATS:<br>• Inputs for data, flags for features<br>• Dependent inputs, services used sometimes<br>• God Component pattern<br>• Asking "what am I today?" instead of just being a list

[3:30 - 4:30]

Here it is. A list component that's trying to do too much.

Inputs for data... but also inputs for sorting flags. Inputs for storage keys. Injected services that are only used sometimes.

Look at persistState. It's a flag. But it drags storageKey, initialState, and a service along with it. They travel together.

This is the God Component pattern. It's asking "What features am I today?" instead of just being a list.

Same crimes as the grid. Now let's take it apart.
-->

---
layout: section
---

# The Real Problem

Coupling isn't bad.

## Hidden coupling is.

<!--
BEATS:<br>• Coupling isn't bad — HIDDEN coupling is<br>• Visible deps (inputs, constructors) = manageable<br>• Buried in flags/templates = loss of control<br>• Question: how to make coupling VISIBLE?<br>• Inputs = default, three escape hatches when they fail

[4:30 - 5:15]

Here's the thing. Coupling isn't bad. Your components HAVE to talk. Data flows. Events propagate. That's fine.

HIDDEN coupling? That's the problem.

When you can see dependencies — in inputs, constructors, imports — you can reason about them. Manage them.

But when coupling is buried in boolean flags? Scattered across templates? That's when you lose control. Change something here, something breaks over there.

So the question isn't "how do I remove coupling?" It's "how do I make coupling VISIBLE?"

That's what we're gonna do. Inputs are your default — stay there as long as you can. But when inputs break down, you've got three escape hatches. Each one makes a different kind of coupling explicit.

[Clear and confident — this is the core idea]
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
BEATS:<br>• Three escape hatches — you know them, trick is WHEN<br>• Inputs = default, visible coupling<br>• Structural flags → Content Projection<br>• Behavioral bundles → Strategy via DI<br>• Composable opt-ins → Directives<br>• We'll spot the tell together

[5:15 - 6:15]

Three escape hatches. You know all of them. The trick is knowing WHEN to reach for each.

Inputs are your default. They create VISIBLE coupling — you can see what a component needs. Stay there as long as you can.

But watch for the tells.

Structural flags — booleans that control what DOM exists? Content projection. Extract the @if.

Behavioral bundles — a flag that drags services and lifecycle code with it? Strategy via DI. Let the injector decide.

Composable opt-ins — behaviors that should stack? Directives. Visible in the template.

For each one, I'll show you the code. We'll spot the tell together.

[Point to each row briefly — then move on]
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

But look closer at **The God List**...

<!--
BEATS:<br>• Boolean flags = feature toggles, component asking "what am I?"<br>• That's the tell — like poker<br>• But truth is deeper: some inputs travel together (storageKey + persistState)<br>• Start simple: one flag isn't like the others

[6:15 - 7:15]

Look at these. Boolean flags. sortable, showHeader, persistState.

Each one's a feature toggle. The component is asking: "What features am I today?"

That's the tell — like in poker. Boolean flags piling up? Component's trying to be too many things.

But here's the thing — and we'll come back to this — boolean flags are just the surface. Look at The God List again. See how storageKey and initialState only matter when persistState is true? They travel together. The truth is deeper.

For now, let's start simple. One of these flags isn't like the others.

[Pause — plant the seed, don't explain yet]
-->

---
layout: default
---

# The Structural Flag

```ts [list.ts]
// From The God List:
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
BEATS:<br>• showHeader is different — controls STRUCTURE, not behavior<br>• @if in template that renders header<br>• Why does LIST decide? That's parent's job<br>• Bonus: cleaner deps — list doesn't import header<br>• Simplest extraction

[7:15 - 7:45]

Let's start with showHeader. This one's different from sortable or persistState.

It doesn't change behavior — it controls structure. What DOM exists.

Look at this. showHeader. And somewhere in the template, an @if that renders a header.

Why does the LIST decide whether a header exists? That's not data. That's not behavior. That's the parent's job.

Bonus: if the parent projects it, the list doesn't need to import the header component. Cleaner dependency graph — list doesn't need to know about header's type at all.

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
BEATS:<br>• Before: showHeader=true, component renders internally<br>• After: no boolean, parent projects or doesn't<br>• @if → ng-content slot, boolean vanishes<br>• Content projection = extracting structural decisions

[7:45 - 8:15]

Watch what happens when you extract the conditional.

Before: showHeader equals true. Component renders header internally.

After: No boolean. Parent just... puts the header there. Or doesn't.

The @if becomes an ng-content slot. The boolean vanishes.

Content projection isn't a "layout feature." It's what happens when you extract structural decisions from components.

[This is the "aha" moment — content projection is removal of conditionals]
-->

---
layout: section
---

# Tool 1: Content Projection

When **structure** varies

Not behavior. Structure.

<!--
BEATS:<br>• Tool 1: Content Projection<br>• When inputs control STRUCTURE (what DOM exists)<br>• Picture frame analogy: frame = shape, you = picture<br>• Component owns layout, you own content

[8:15 - 8:45]

Tool one. Content projection.

First escape hatch. Use this when inputs control STRUCTURE — what DOM exists, not what it does.

Think picture frame. Frame decides shape and size. You choose the picture.

Component owns layout. You own content.

[Quick — set up the code]
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
BEATS:<br>• Two slots: header + default<br>• "I'll handle wrapper, you decide contents"<br>• Cards, modals, panels — shell consistent, contents vary

[8:45 - 9:15]

Two ng-content slots. Header and default.

Component says: "I'll handle the wrapper. You decide what goes inside."

Cards, modals, panels. Shell stays consistent, contents vary.

[Quick — code speaks for itself]
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
BEATS:<br>• showHeader handled — one down<br>• But persistState + friends (services, inputs)?<br>• Can't ng-content an HttpClient<br>• Content projection = DOM nodes only<br>• WHAT HAPPENS vs WHAT APPEARS → different tool

[9:15 - 9:45]

So we handled showHeader. Boolean's gone. One down.

But look back at The God List. What about persistState? And its friends — storageKey, initialState, the services?

Can we ng-content an HttpClient? Put localStorage in a slot?

[Let them laugh]

No. Content projection is for DOM nodes. Visual stuff.

When variation is about WHAT HAPPENS, not WHAT APPEARS — that's a different tell. Different escape hatch.

[Transition to strategy]
-->

---
layout: section
---

# Tool 2: Strategy via DI

**A** or **B**

Never both.

<!--
BEATS:<br>• Tool 2: Strategy via DI<br>• A or B, never both<br>• Server OR localStorage, real OR mock<br>• Component doesn't know which — DI provides<br>• Zero if-statements, decision lives elsewhere

[9:45 - 10:15]

Tool two. Strategy via DI.

A or B. Pick one. Never both at the same time.

Server OR localStorage. Real API OR mock. One implementation active.

Component doesn't know which. Just says "I need something that can save." DI provides the right one.

No if-statements in the component. Decision lives elsewhere.

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
BEATS:<br>• Truth deeper than flags — this is it<br>• persistState + storageKey + initialState + services + lifecycle<br>• All only matter if persistState=true — they travel together<br>• Can't content-project behavior<br>• What do we do when a BUNDLE needs to move?

[10:15 - 11:00]

Remember earlier I said the truth is deeper than boolean flags?

This is it. Look at the code again — just the persistence part.

One flag — persistState. But it's not alone.

storageKey — WHERE to save. initialState — what to restore. stateSaved — notify when done.

Then the services. ApiService for server. StorageService for browser. And the lifecycle code.

All of this only matters if persistState is true. They travel together. They're a behavioral unit.

Key insight: you can't content-project behavior. Can't put a service in an ng-content slot.

So what do we do when a whole bundle of behavior needs to move?

[This is the reveal — set up Strategy]
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
BEATS:<br>• Bundle needs to move OUT<br>• WHERE depends on context: Admin=server, Public=localStorage, Preview=noop<br>• Same component, different behavior<br>• Old: more booleans. New: component doesn't know/care

[11:00 - 11:30]

That whole bundle — flag, inputs, services, lifecycle code — needs to move OUT of the component.

But WHERE it goes? Depends on context.

Admin dashboard? Save to server. Public view? localStorage. Preview mode? Don't save at all.

Same component. Different storage behavior.

Old way: another boolean, another if-statement. New way: component doesn't know. Doesn't care.

[Quick — set up the solution]
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
BEATS:<br>• Interface: save() and load() — WHAT, not HOW<br>• InjectionToken = lookup key<br>• (Promise for slides, Observable works too)<br>• Just the contract, no implementation yet

[11:30 - 11:45]

Step one: define what we need. save() and load(). Not HOW — just that it CAN.

InjectionToken is our lookup key.

Note: I'm using Promise to keep slides clean. You can use Observable too — same pattern, different async primitive.

No implementation yet. Just the contract.

[Fast — setup code]
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
BEATS:<br>• LocalStorageStrategy — browser storage, works offline

[11:45 - 12:00]

First implementation. LocalStorageStrategy.

Browser storage. Synchronous. Works offline.

[Quick — move to next]
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
BEATS:<br>• ServerStorageStrategy — API call, syncs across devices<br>• From outside? Identical. List doesn't know which<br>• Could add NoopStrategy for preview

[12:00 - 12:30]

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
BEATS:<br>• Decision lives in the PROVIDER<br>• Key: provider scoped to subtree — hierarchical injection<br>• Zero if-statements in component<br>• Pro tip: NoopStrategy at root, override per context<br>• Strategy = mutually exclusive. Opt-in composition = directives<br>• THAT's visible coupling

[12:30 - 13:15]

Here's where the decision lives. In the provider.

AdminDashboard says: "In my subtree, use ServerStorageStrategy."

Key insight: this provider is scoped to AdminDashboard's subtree — not global. That's the magic of hierarchical injection.

List injects STORAGE_STRATEGY. Gets the server version. Zero if-statements.

Different page? Provides LocalStorageStrategy. Same list, different behavior.

Pro tip: provide NoopStorageStrategy at root, override where needed. Safe by default, customized per context.

```ts
// The third implementation — does nothing, safely
export class NoopStorageStrategy implements StorageStrategy {
  async save() {}
  async load() { return null; }
}
```

List never changes. New mode? Write new strategy, provide it somewhere. List doesn't know, doesn't care.

Important: Strategy is for mutually exclusive implementations. One active at a time. If you want opt-in composition — adding behaviors, not swapping them — that's what directives are for.

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
BEATS:<br>• Problem: feature set invisible in template<br>• Strategy = SWAP, but what about OPTIONAL?<br>• [CLICK MEME]<br>• Need behaviors that ADD, not swap<br>• Strategy swaps, directives add

[13:15 - 14:00]

But look at this. What's the problem?

Strategy pattern is great for A or B — you SWAP implementations. But what if sorting is optional? What if filtering is optional?

Three strategies injected. All three always present. You can swap implementations — but you can't opt out. Strategy is for swapping, not adding.

[Click for meme]

Yeah, you could make injection optional. But then you're riddled with null checks. "If sorter exists, sort. If filter exists, filter."

We need behaviors that ACCUMULATE — you ADD them, not swap them. And here's the key — visible in the template. Look at the HTML and know exactly what this list does.

That's the third tell. Composable opt-ins. Strategy swaps, directives add.

[Transition]
-->

---
layout: section
---

# Tool 3: Directives

What's **left** after the right extractions.

The smallest unit of reusable behavior.

<!--
BEATS:<br>• Tool 3: Directives<br>• showHeader → projection. persistState → strategy. What's left? sortable<br>• Not structure, not a bundle — just a behavior. Present or absent<br>• Directive = smallest unit that survived extraction<br>• Directives aren't "another tool" — they're what's LEFT

[14:00 - 14:45]

Tool three. Directives.

Look back at the list. We handled showHeader — content projection now. We handled persistState and its bundle — strategy now.

What's left? sortable.

Doesn't control structure. Isn't a bundle of dependent inputs and services. It's just... a behavior. Either the list is sortable, or it's not.

No branching. No context questions. Present or absent.

That's a directive. Smallest unit of reusable behavior that survived extraction.

Directives aren't "yet another tool." They're what's LEFT when you do the other extractions right.

[Reframe — directives are inevitable, not arbitrary]
-->

---
layout: default
---

# Directive: Sortable

```ts [sortable.ts]
@Directive({ selector: 'app-list[sortable]' })
export class Sortable {
  #list = inject(ListComponent);

  sortKey = input<string>();
  sortDir = input<'asc' | 'desc'>('asc');

  constructor() {
    effect(() => {
      const items = this.#list.items();
      const key = this.sortKey();
      const dir = this.sortDir();
      if (!key) return;

      const sorted = [...items].sort((a, b) =>
        dir === 'asc' ? a[key] - b[key] : b[key] - a[key]
      );
      this.#list.displayItems.set(sorted);
    });
  }
}
```

Directive owns sort config. List just displays.

<!--
BEATS:<br>• Selector: app-list[sortable] — no attribute = doesn't exist<br>• Directive owns inputs (sortKey, sortDir)<br>• List exposes displayItems signal — directive writes to it<br>• Selector makes coupling explicit

[14:45 - 15:15]

SortableDirective. Selector targets app-list with sortable attribute.

No attribute? Directive doesn't exist. Zero overhead.

Notice: the directive owns its own inputs — sortKey, sortDir. The list just exposes a displayItems signal that the directive writes to.

inject(ListComponent) on the same element resolves the host instance. Yeah, directive knows the component's internal API. That's okay — they're designed together. The selector makes it explicit.

Effect reacts to items AND sort config. Change either, list re-sorts.

[Quick — show the pattern]
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
BEATS:<br>• PersistDirective — same pattern<br>• KEY: injects STORAGE_STRATEGY — tools layer together!<br>• Strategy = WHERE/HOW (server vs localStorage)<br>• Directive = WHEN (on dirty state changes)<br>• Each tool has single job — easy to test, easy to compose

[15:15 - 15:45]

PersistDirective. Same pattern.

But notice — it injects STORAGE_STRATEGY. Key insight: we're decoupling persistence into two axes.

Strategy answers: WHERE and HOW do we store? Server vs localStorage vs noop.
Directive answers: WHEN do we store? On state changes, only when dirty.

Yeah, this effect causes I/O. In production I'd route it through a debounced queue with cancellation. But the decomposition is what matters: WHERE lives in strategy, WHEN lives in directive.

Tools layer together. Each one has a single job. Easy to test alone, easy to compose.

[Point out strategy injection — this is the payoff]
-->

---
layout: default
---

# Directives: Usage

```html [app.html]
<!-- Simple -->
<app-list [items]="data" />

<!-- With sorting -->
<app-list sortable sortKey="name" [items]="data" />

<!-- Full-featured -->
<app-list sortable sortKey="date" sortDir="desc"
          filterable persistable [items]="data" />
```

**Visible in the template.** Look at the HTML, know what it does.

<!--
BEATS:<br>• Simple: no directives. With sorting: sortable + inputs. Full: all three<br>• Key: VISIBLE in template — look at HTML, know what it does<br>• Each page picks its combo — opt-in complexity

[15:45 - 16:15]

Look at the template.

Simple page? No directives. Just a list.

Need sorting? Add sortable. The directive owns its inputs — sortKey, sortDir. List doesn't care.

Full-featured? All three directives. Each one brings its own configuration.

Key insight: it's all visible in the template. New developer opens this file, immediately knows what this list does.

[Fast — template speaks for itself]
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
BEATS:<br>• Three pages, same three directives — copy-pasted<br>• Coupling hidden in REPETITION<br>• Add a fifth? Hunt every page. Miss one? Drift<br>• [CLICK MEME]<br>• Same combo 3x = concept without a name

[16:15 - 17:00]

But look at this. What do you see?

Three pages. Same three directives. Copy-pasted.

Coupling is hidden in REPETITION. "We always use these three together."

What happens when we add a fifth? Hunt down every page. Update all of them. Miss one? Now they drift apart.

[Click for meme]

Same combination three times. Not coincidence. That's a concept without a name.

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
BEATS:<br>• 1x = code. 2x = coincidence. 3x = concept — NAME IT<br>• Caveat: same meaning, same reason. Different reasons = don't bundle<br>• Named = talk about it, document, test, evolve in ONE place<br>• Unnamed patterns drift (story: filtering disabled "temporarily")<br>• 3x same meaning? Name it

[17:00 - 17:45]

The promotion rule.

Once: just code.
Twice: maybe coincidence.
Three times: that's a concept. Give it a name.

Important caveat: three times with the SAME meaning, the SAME reason. If reasons differ, don't bundle. That's false duplication.

When you name something, you can talk about it. Document it. Test it. Evolve it in ONE place.

Unnamed patterns drift. We had three "full-featured lists." One had filtering disabled "temporarily." Six months later? Nobody knew why.

See it three times with the same meaning? Name it.

[Deliver with conviction — this is memorable]
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
BEATS:<br>• hostDirectives — bundles directives<br>• PowerList: one attribute, three behaviors<br>• Body is EMPTY — just composition, a name for a pattern<br>• Add fourth behavior? One file. Done

[17:45 - 18:15]

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
BEATS:<br>• Before: 3 attributes. After: 1 word — powerList<br>• [CLICK MEME]<br>• Functionally identical, conceptually worlds apart<br>• New dev sees "powerList" — immediately gets it<br>• List of things → concept

[18:15 - 18:30]

Before: three attributes. After: one word. powerList.

[Click for meme]

Functionally identical. Conceptually? Worlds apart.

New developer sees "powerList" — immediately understands what this list does.

First version is a list of things. Second is a concept.

[Brief — slide is clear]
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

<!--
BEATS:<br>• Coordinator directive — when directives must work together<br>• DirtyTrackable + Debounceable = independent<br>• AutoSaveable coordinates: when dirty, debounce save<br>• Intentional coupling? Give it a home — named, testable

[18:30 - 19:00]

One more pattern. Coordinator directive.

Remember the dirty guard in Persistable? That was the simple version. In real code, you also want debouncing, cancellation of in-flight saves, etc.

Product says: "auto-save when dirty, but debounce it."

DirtyTrackable and Debounceable are independent directives. Neither knows about the other.

AutoSaveable brings both in, coordinates them. When dirty, debounce the save.

Intentional coupling? Give it a home. One place. Named. Testable.

[Keep it brief — they get it]
-->

---
layout: section
---

# When NOT to Use These

Each tool has limits.

<!--
BEATS:<br>• Quick guardrails — when NOT to use

[19:00 - 19:15]

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
BEATS:<br>• Inputs = CHEAP, escape hatches = EXPENSIVE<br>• Don't reach for Tool 3 when inputs work<br>• Content projection: structure only<br>• Strategies: not for optional behaviors<br>• Directives: don't repeat bundles — promote<br>• hostDirectives: bundle concepts, not unrelated things

[19:15 - 19:30]

Quick summary.

Important mental model: Inputs are CHEAP — easy to read and debug. Escape hatches are EXPENSIVE — more files, more indirection, debugging jumps across DI boundaries and directive composition.

Don't reach for Tool 3 when inputs still work. Complexity should earn its keep.

Content projection: structure only, not behavior.
Strategies: not when behaviors should be optional.
Directives: great, but don't repeat the same bundle across pages — promote it.
hostDirectives: don't bundle unrelated things just to save typing. Bundle concepts that belong together.

[Crisp — they have the framework]
-->

---
layout: default
---

# Migration Path

From God Component to clean architecture:

1. **Extract** - Pull behaviors out of the god component
2. **Interface** - Define contracts for swappable behaviors
3. **Compose** - Make remaining behaviors optional with directives
4. **Name** - Bundle common patterns with hostDirectives

<!--
BEATS:<br>• 1. Extract — pull behaviors out, don't worry about perfection<br>• 2. Interface — mutually exclusive → strategies<br>• 3. Compose — remaining → optional directives<br>• 4. Name — 3x → hostDirectives<br>• One behavior at a time

[19:30 - 20:15]

So you've got a God Component. How do you fix it?

One: Extract. Pull behaviors out. Don't worry about perfection. Just get them out of the god component.

Two: Interface. Which behaviors are mutually exclusive? Those become strategies.

Three: Compose. Remaining behaviors become optional directives.

Four: Name. See it three times? hostDirectives.

Don't do it all at once. One behavior at a time. Each extraction makes the component lighter.

[Practical — this is Monday morning advice]
-->

---
layout: default
---

# The Matrix

| Feature | Main Page | Drawer A | Drawer B |
|---------|-----------|----------|----------|
| Expand/collapse | ✓ | ✓ | ✓ |
| Row styles | ✓ | ✓ | ✓ |
| Context menu | ✓ | ✗ | ✓ |
| Column wizard | ✗ | ✓ | ✗ |
| SSRM fetching | ✗ | ✗ | ✓ |

**Baseline** = always there. **The rest** = opt-in.

<!--
BEATS:<br>• Remember the grid? Here's how we fixed it<br>• Made a matrix — feature x occurrence<br>• Found baseline, rest became Strategy or Directive<br>• Show of hands payoff

[20:15 - 21:45]

Remember the grid from the start? Show of hands — who's worked on a component like that?

[Same hands as before]

Here's how we fixed it.

We made a matrix. Every feature, every place we used the grid.

Expand/collapse? Everywhere. Row styles? Everywhere. That's baseline — stays in the component.

Context menu? Some places — directive. Column wizard? One place — directive. SSRM fetching? One context only — strategy.

The second drawer? Same grid, just different directives applied. Third drawer? Same grid, SSRM strategy provided.

This framework isn't theory. It's what we actually shipped. The grid works now.

[Payoff — connect back to opening hands]
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
BEATS:<br>• Inputs = default, visible coupling = manageable<br>• Structural flags → Content Projection (extract @if)<br>• Behavioral bundles → Strategy via DI (injector decides)<br>• Composable opt-ins → Directives (visible in template)<br>• 3x same meaning → hostDirectives<br>• Your job: notice tell, pick escape hatch

[21:45 - 22:30]

Here's everything together.

Inputs are your default. Stay there as long as you can. They create visible coupling — that's manageable.

But when you see these tells, reach for the escape hatch:

Structural flags — booleans controlling what DOM exists? Content projection. Extract the @if.

Behavioral bundles — flag dragging services and lifecycle code? Strategy via DI. Let the injector decide.

Composable opt-ins — behaviors that should stack, present or absent? Directives. Visible in template.

Same combo three times, same meaning? Name it with hostDirectives.

Each row is a response to a tell. Your job: notice the tell. Pick the escape hatch.

[Pause — let them photograph it]
-->

---
layout: section
---

# The Takeaway

Good abstractions aren't chosen.

## They're discovered.

<img src="/assets/qr-slides.png" class="absolute bottom-8 right-8 w-28 opacity-80" />

<!--
BEATS:<br>• We started with "just one more input"<br>• Now you have a framework: spot the tell, pick the escape hatch<br>• Good abstractions aren't chosen — DISCOVERED<br>• Next time you see isAdmin or showCompactMode — you'll know

[22:30 - 23:15]

Final thought.

Remember at the start — "just one more input"? That's where every God Component begins.

Now you have a framework. Spot the tell. Pick the escape hatch.

Structural flag? Content projection.
Behavioral bundle? Strategy.
Composable opt-in? Directive.
Same combo three times? Name it.

Good abstractions aren't chosen at project kickoff. They're discovered in the code that's already there.

Next time you see isAdmin, showCompactMode, persistState... you'll know what the code is telling you.

Listen to it.

[Pause — let this land]
-->

---
layout: end
---

# Thank You

<div class="flex items-center gap-6 mt-4">
  <img src="/assets/profile.jpg" class="w-20 h-20 rounded-full object-cover" alt="Dor Peled" />
  <div>
    <div class="font-bold text-xl">Dor Peled</div>
    <div class="opacity-80">Software Engineer @ Coralogix</div>
    <div class="flex gap-4 mt-3 text-lg">
      <a href="https://github.com/Knat-Dev" target="_blank" class="opacity-75 hover:opacity-100"><carbon-logo-github /></a>
      <a href="https://linkedin.com/in/dorpeled" target="_blank" class="opacity-75 hover:opacity-100"><carbon-logo-linkedin /></a>
      <a href="https://twitter.com/Knat_Dev" target="_blank" class="opacity-75 hover:opacity-100"><carbon-logo-x /></a>
    </div>
  </div>
</div>

Questions?

<!--
BEATS:<br>• Thank you<br>• Component fighting back? Come find me<br>• Questions?

[23:15 - 24:15]

That's it. Thank you.

Got a component that's fighting back? Come find me. Or if you just wanna talk Angular, decoupling, why your component has 47 inputs... I'm around.

Questions?

[Open, approachable — real learning happens now]
-->
