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

You know those moments when you look at code and think "this feels wrong, but I can't explain why"? That's what we're hunting for. The signals.

We all know the tools - inputs, outputs, content projection, DI, directives. The hard part isn't knowing WHAT they are. It's knowing WHEN to reach for each one.

So watch the code with me. When you see something that makes you uncomfortable - that's the signal.

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

Angular codebases are the same. They don't fail immediately. They accumulate complexity quietly. One feature at a time. And if you don't stay with them long enough, you miss the signals.

So this talk isn't best practices. It's pattern recognition. I'll show you what I've learned to look for - and you tell me if you see it too.

[Keep this tight - 45 seconds]
-->

---
layout: default
---

# Month 1

```typescript
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

Month one. Two inputs - items, loading. Look at this code. What do you think?

Clean, right? Simple. Easy to test. This is good code.

And it IS good code. Right now, this is exactly the right abstraction.

Enjoy this moment. It won't last.

[Brief pause - let them appreciate the calm before the storm]
-->

---
layout: default
---

# Month 6

```typescript {4-7}
@Component({ selector: 'app-list' })
export class ListComponent {
  items = input<Item[]>([]);
  sortable = input(false);
  filterable = input(false);
  persistState = input(false);
  // ... and it keeps growing
}
```

<v-click>
<img src="/assets/this-is-fine.jpg" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-120 rounded-lg shadow-xl" />
</v-click>

<!--
[2:45 - 4:00]

Month six. Product shows up. "Can we add sorting?" Sure. "Filtering?" Okay. "Remember state?" Fine.

Now look at this code. What do you see?

Boolean flags. sortable, filterable, persistState. And somewhere in the template, there's an ngIf for each one.

We didn't inherit this mess. We built it. One reasonable feature request at a time.

[Click for meme]

Show of hands - who's worked on a component like this?

[Pause for hands - acknowledge them]

Yeah. Me too. So what went wrong?

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

That's what we're going to do. Four tools. Each one makes a different kind of coupling explicit.

[Clear and confident - this is the core idea]
-->

---
layout: image-right
---

::left::

# Four Tools

The trick is knowing **when**.

::default::

| Tool               | When                    |
| ------------------ | ----------------------- |
| Inputs/Outputs     | Parent configures child |
| Content Projection | Structure varies        |
| Strategy via DI    | A **or** B              |
| Directives         | A **and** B **and** C   |

<!--
[5:00 - 6:00]

Four tools. You know all of them. The trick is recognizing WHEN.

Inputs/outputs - the default. Stay here as long as you can.

Content projection - when STRUCTURE varies. Different things in different slots.

Strategy via DI - A OR B. Pick one. Never both.

Directives - A AND B AND C. Mix and match.

For each tool, I'll show you the code, and we'll spot the signal together. The moment when the code says "I need something different."

Ready? Let's start with inputs.

[Point to each row briefly - then move on]
-->

---
layout: section
---

# Tool 1: Inputs/Outputs

**Parent** decides WHAT

**Child** decides HOW

<!--
[6:00 - 6:30]

Tool one. Your default. Stay here as long as you can.

Parent decides WHAT to show. Child decides HOW to show it.

Inputs create VISIBLE coupling. You can see what a component needs. That's manageable.

But watch for the moment when inputs start asking the wrong question.

[Quick - they know this]
-->

---
layout: default
---

# The Signal

```typescript {1-2,4-5}
readonly isAdmin = input(false);
readonly isCompactMode = input(false);

if (this.isAdmin()) { /* show delete */ }
if (this.isCompactMode()) { /* hide columns */ }
```

Component asking _"who's using me?"_ via isAdmin, isCompactMode

<!--
[6:30 - 7:30]

Look at this code. What's wrong here?

isAdmin. isCompactMode. These aren't data. These are context flags. The component is asking: "WHO is using me?"

See the problem? Every new context means touching this component. Admin context. Mobile context. Preview context. The component has to know about all of them.

The code is saying: "I don't want to know who's using me. Just tell me what to do."

That's the signal. When inputs ask WHO instead of WHAT - time for different tools.

[Pause - let them internalize the WHO vs WHAT distinction]
-->

---
layout: section
---

# Tool 2: Content Projection

When **structure** varies

Not behavior. Structure.

<!--
[7:30 - 8:00]

Tool two. Content projection.

Use this when the STRUCTURE varies. Not behavior - structure. What goes where. What elements in which slots.

Think picture frame. Frame decides shape and size. You choose the picture.

Component owns layout. You own content.

[Quick - set up the code]
-->

---
layout: default
---

# Content Projection

```html
<div class="header">
  <ng-content select="[header]" />
</div>
<ng-content />
```

Card owns layout. Consumer owns content via ng-content.

<!--
[8:00 - 8:30]

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
- _"Sort ascending vs descending"_

You need different **behavior**, not structure.

<!--
[8:30 - 9:00]

But here's when content projection doesn't work.

"Save to localStorage vs server" - can't ng-content a storage mechanism.

"Sort ascending vs descending" - that's behavior, not structure.

Content projection is for DOM nodes. Visual stuff. When variation is about WHAT HAPPENS, not WHAT APPEARS - that's tool three.

[Transition to strategy]
-->

---
layout: section
---

# Tool 3: Strategy via DI

**A** or **B**

Never both.

<!--
[9:00 - 9:30]

Tool three. Strategy via DI.

A or B. Pick one. Never both at the same time.

Server OR localStorage. Real API OR mock. One implementation active.

Component doesn't know which. It just says "I need something that can save." DI provides the right one.

No if-statements in the component. The decision lives elsewhere.

Let me show you what this looks like.

[Set up the example]
-->

---
layout: image-right
---

::left::

# Strategy: The Problem

Component persists state. But **where**?

::default::

| Context | Storage      |
| ------- | ------------ |
| Admin   | Server       |
| Public  | localStorage |
| Preview | Don't save   |

<!--
[9:30 - 10:00]

Back to our component. It needs to persist state. But WHERE depends on context.

Admin? Server. Public? localStorage. Preview? Don't save.

Old way: storageMode input. If-statements everywhere.

New way: Component says "I need to save." Doesn't care where.

[Quick - set up the solution]
-->

---
layout: default
---

# Strategy: The Interface

```typescript
export interface StorageStrategy {
  save(key: string, data: unknown): void;
  load(key: string): unknown | null;
}

export const STORAGE_STRATEGY =
  new InjectionToken<StorageStrategy>('StorageStrategy');
```

<!--
[10:00 - 10:20]

Step one: define what we need. save() and load(). Not HOW - just that it CAN.

InjectionToken is our lookup key.

No implementation yet. Just the contract.

[Fast - setup code]
-->

---
layout: default
---

# Strategy: LocalStorage

```typescript
export class LocalStorageStrategy
  implements StorageStrategy
{
  save(key: string, data: unknown) {
    localStorage.setItem(key, JSON.stringify(data));
  }
  load(key: string) {
    const item = localStorage.getItem(key);
    return item ? JSON.parse(item) : null;
  }
}
```

Uses localStorage. Works offline.

<!--
[10:20 - 10:35]

First implementation. LocalStorageStrategy.

Browser storage. Synchronous. Works offline.

[Quick - move to next]
-->

---
layout: default
---

# Strategy: Server

```typescript
export class ServerStorageStrategy
  implements StorageStrategy
{
  #http = inject(HttpClient);

  save(key: string, data: unknown) {
    this.#http
      .post('/api/preferences', { key, data })
      .subscribe();
  }
  load(key: string) {
    return firstValueFrom(
      this.#http.get(`/api/preferences/${key}`)
    );
  }
}
```

Uses HttpClient. Syncs across devices.

<!--
[10:35 - 11:00]

Second implementation. ServerStorageStrategy.

API call. Syncs across devices.

From the outside? Identical. Grid calls save() and load(). Doesn't know which one it got.

Could add NoopStrategy for preview. Same interface, does nothing.

[Keep moving]
-->

---
layout: default
---

# Strategy: The Provider

```typescript
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
[11:00 - 11:45]

Here's where the decision lives. In the provider.

AdminDashboard says: "In my subtree, use ServerStorageStrategy."

Grid injects STORAGE_STRATEGY. Gets the server version. Zero if-statements.

Different page? Provides LocalStorageStrategy. Same grid, different behavior.

The grid never changes. New mode? Write new strategy, provide it somewhere. Grid doesn't know, doesn't care.

THAT's visible coupling. You can see exactly what behavior each context gets.

[Emphasize: zero if-statements, visible coupling]
-->

---
layout: default
---

# The Cue

```typescript
export class ListComponent {
  #storage = inject(STORAGE_STRATEGY);
  #sorter = inject(SORT_STRATEGY);
  #filter = inject(FILTER_STRATEGY);
}
```

Can't opt out. All three are injected.

<v-click>
<img src="/assets/one-does-not-simply.jpg" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-120 rounded-lg shadow-xl" />
</v-click>

<!--
[11:45 - 12:30]

But look at this code. What's the problem?

Three strategies injected. You can SWAP them. But you can't OPT OUT.

Every consumer gets all three. Can't say "I don't need sorting on this page."

[Click for meme]

See the signal? When you're injecting strategies and half should be optional? Wrong tool.

We need behaviors that ACCUMULATE. Add what you want, skip what you don't.

That's tool four.

[Transition]
-->

---
layout: section
---

# Tool 4: Directives

**A** and **B** and **C**

Behaviors accumulate.

<!--
[12:30 - 13:00]

Tool four. Directives.

A AND B AND C. Behaviors stack.

Strategies: pick one. Directives: as many as you want. Sorting AND filtering AND persistence. Or just sorting. Your choice.

Each behavior lives in its own directive. Attach to the same element, each adds functionality.

Component stays minimal. Directives add superpowers.

[Quick - show the code]
-->

---
layout: default
---

# Directive: Sortable

```typescript
@Directive({ selector: 'app-list[sortable]' })
export class SortableDirective {
  #list = inject(ListComponent);

  constructor() {
    effect(() => this.#list.sort());
  }
}
```

Only activates with the sortable attribute.

<!--
[13:00 - 13:30]

SortableDirective. Selector targets app-list with sortable attribute.

No attribute? Directive doesn't exist. Zero overhead.

Injects the component, adds behavior.

[Quick - show the pattern]
-->

---
layout: default
---

# Directive: Persist

```typescript
@Directive({ selector: 'app-list[persist]' })
export class PersistDirective {
  #list = inject(ListComponent);
  #storage = inject(STORAGE_STRATEGY);

  constructor() {
    effect(() => this.#storage.save('state', this.#list.state()));
  }
}
```

Notice: injects STORAGE_STRATEGY. **Tools layer together.**

<!--
[13:30 - 14:00]

PersistDirective. Same pattern.

But notice - it injects STORAGE_STRATEGY. Tools layer together. Directive decides WHEN to save. Strategy decides WHERE.

Each directive: one job. Easy to test alone.

[Point out strategy injection - tools compose]
-->

---
layout: default
---

# Directives: Usage

```html
<!-- Simple -->
<app-list sortable [items]="data" />

<!-- Full-featured -->
<app-list sortable filterable persist [items]="data" />
```

Add sortable, filterable, persist as needed.

<!--
[14:00 - 14:30]

In the template.

Simple page? Just sortable. Full-featured? All three.

Each page picks its combination. Opt-in complexity.

[Fast - template speaks for itself]
-->

---
layout: default
---

# The Sign

```html
<!-- Page A -->
<app-list sortable filterable persist [items]="a" />

<!-- Page B -->
<app-list sortable filterable persist [items]="b" />

<!-- Page C -->
<app-list sortable filterable persist [items]="c" />
```

Same combo. Three times.

<v-click>
<img src="/assets/distracted-boyfriend.jpg" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-120 rounded-lg shadow-xl" />
</v-click>

<!--
[14:30 - 15:15]

But look at this code. What do you see?

Three pages. Same four directives. Copy-pasted.

The coupling is hidden in REPETITION. "We always use these four together."

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
[15:15 - 16:00]

The promotion rule.

Once: just code.
Twice: maybe coincidence.
Three times: that's a concept. Give it a name.

When you name something, you can talk about it. Document it. Test it. Evolve it in ONE place.

Unnamed patterns drift. We had three "full-featured grids." One had filtering disabled "temporarily." Six months later? Nobody knew why.

See it three times? Name it.

[Deliver with conviction - this is memorable]
-->

---
layout: default
---

# hostDirectives

```typescript
@Directive({
  selector: '[powerList]',
  hostDirectives: [
    SortableDirective,
    FilterableDirective,
    PersistDirective,
  ],
})
export class PowerListDirective {}
```

One attribute. Three behaviors.

<!--
[16:00 - 16:45]

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

```html
<!-- Before: 3 attributes -->
<app-list sortable filterable persist [items]="data" />

<!-- After: 1 named concept -->
<app-list powerList [items]="data" />
```

<v-click>
<img src="/assets/pam-theyre-different.jpg" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-120 rounded-lg shadow-xl" />
</v-click>

<!--
[16:45 - 17:15]

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

```typescript
@Directive({
  selector: '[autoSave]',
  hostDirectives: [DirtyTrackingDirective, DebounceDirective],
})
export class AutoSaveDirective {
  #dirty = inject(DirtyTrackingDirective);
  #debounce = inject(DebounceDirective);

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
[17:15 - 18:00]

One more pattern. Coordinator directive.

Sometimes two behaviors MUST work together. Product says: "auto-save when dirty, but debounce it."

DirtyTracking and Debounce are independent directives. Neither knows about the other.

AutoSaveDirective brings both in, coordinates them. When dirty, debounce the save.

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
[18:00 - 18:15]

Quick guardrails. When NOT to use each tool.

[Fast section]
-->

---
layout: default
---

# Anti-Patterns

| Tool               | Don't use when...                       |
| ------------------ | --------------------------------------- |
| Inputs/Outputs     | Component asks "who's using me?"        |
| Content Projection | You need behavior, not structure        |
| Strategy via DI    | Behaviors should be optional/composable |
| Directives         | Same combo appears 3+ times             |
| hostDirectives     | Behaviors are truly independent         |

<!--
[18:15 - 18:45]

Quick summary.

Inputs: stop when you're asking WHO, not WHAT.
Content projection: structure only, not behavior.
Strategies: not when behaviors should be optional.
Directives: stop when same combo appears 3x.
hostDirectives: only bundle things that belong together.

[Crisp - they have the framework]
-->

---
layout: default
---

# Testing Impact

```typescript
// Inputs: Easy isolation
const fixture = TestBed.createComponent(DataGrid);
fixture.componentRef.setInput('data', mockData);

// Strategy: Mock the interface
TestBed.configureTestingModule({
  providers: [
    { provide: STORAGE_STRATEGY, useValue: mockStorage },
  ],
});

// Directives: Test with and without
const withSorting =
  '<app-data-grid sortable [data]="items" />';
const withoutSorting = '<app-data-grid [data]="items" />';
```

<!--
[18:45 - 19:15]

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

From Month 6 mess to clean architecture:

1. **Extract** - Pull behaviors out of the god component
2. **Interface** - Define contracts for swappable behaviors
3. **Compose** - Make remaining behaviors optional with directives
4. **Name** - Bundle common patterns with hostDirectives

<!--
[19:15 - 20:00]

So you've got a Month 6 mess. How do you fix it?

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

What is the code telling you?

::default::

| Signal                  | Tool               |
| ----------------------- | ------------------ |
| Parent configures child | Inputs/Outputs     |
| Boolean flags           | Time to graduate   |
| Structure varies        | Content Projection |
| A or B                  | Strategy via DI    |
| A and B and C           | Directives         |
| Same combo 3x           | hostDirectives     |

<!--
[20:00 - 20:45]

Here's everything together.

What is the code telling you?

Parent configures child? Inputs. Stay here as long as you can.
Boolean flags creeping in? Time to graduate.
Structure varies? Content projection.
A OR B? Strategy.
A AND B AND C? Directives.
Same combo three times? Name it.

Each row is a response to a signal. Your job: notice the signal. Pick the tool.

[Pause - let them photograph it]
-->

---
layout: section
---

# The Takeaway

Good abstractions aren't chosen.

## They're discovered.

<!--
[20:45 - 21:30]

Final thought.

We talk about "choosing the right abstraction" like there's a menu at project start. That's not how it works.

Good abstractions aren't chosen. They're discovered.

You write code. Watch it evolve. Notice where it struggles. The code tells you - through boolean flags, repetition, god components - what it needs.

Your job isn't to predict the future. It's to listen to the present. Notice the signals.

Next time you see isAdmin or isCompactMode in a component... you'll know what the code is telling you.

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
[21:30 - 22:00]

That's it. Thank you.

If you've got a component that's fighting back - come find me. Or if you just want to talk Angular, decoupling, or why your component has 47 inputs... I'm around.

Questions?

[Open, approachable - real learning happens now]
-->
