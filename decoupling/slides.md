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

**Dor Peled** · @Knat-Dev

<!--
Hey everyone, I'm Dor. Software engineer at Coralogix.

Today I want to share a mental model for when to use different decoupling tools in Angular.
-->

---
layout: section
---

# Month 1

Clean. Simple. Works great.

<!--
We've all been here. Month one, you build a component. Three inputs, maybe an output. Life is good.
-->

---
layout: section
---

# Month 6

*"Why is this so hard to change?"*

<!--
Month six. Product wants a variation. And you realize... this thing is stuck. Every change breaks something else.

We didn't inherit this mess. We built it. One reasonable feature at a time.
-->

---
layout: section
---

# The Real Problem

Coupling isn't bad.

## Hidden coupling is.

<!--
Coupling isn't bad. Every system has coupling. The problem is when coupling is HIDDEN. When you can't see it, you can't manage it.
-->

---
layout: default
---

# Four Tools

| Tool | When |
|------|------|
| Inputs/Outputs | Parent configures child |
| Content Projection | Structure varies |
| Strategy via DI | A **or** B |
| Directives | A **and** B **and** C |

<!--
Four tools. Each solves a different problem. The trick is recognizing WHEN to use each one.
-->

---
layout: section
---

# Tool 1: Inputs/Outputs

**Parent** decides WHAT

**Child** decides HOW

<!--
Everyone's first tool. Parent passes data down, child emits events up. Stay here as long as possible.
-->

---
layout: default
---

# The Smell

```typescript
readonly isAdmin = input(false);
readonly isCompactMode = input(false);

if (this.isAdmin()) { /* show delete */ }
if (this.isCompactMode()) { /* hide columns */ }
```

Component asking *"who's using me?"*

<!--
Boolean flags. The component is asking 'who's using me?' and changing behavior based on the answer.

When you see if-branches based on context flags, inputs have outgrown their welcome.
-->

---
layout: section
---

# Tool 2: Content Projection

When **structure** varies

Not behavior. Structure.

<!--
The parent passes structure in, not configuration down. Use this when the variation is about WHAT goes WHERE.
-->

---
layout: default
---

# Content Projection

```typescript
template: `
  <div class="header"><ng-content select="[header]" /></div>
  <ng-content />
`
```

Card owns layout. Consumer owns content.

<!--
The card owns layout. The consumer owns content. Different consumers can put different things in.
-->

---
layout: default
---

# The Tell

Doesn't work for:

- *"Save to localStorage vs server"*
- *"Sort ascending vs descending"*

You need different **behavior**, not structure.

<!--
If you're trying to ng-content a function... you've gone too far.
-->

---
layout: section
---

# Tool 3: Strategy via DI

**A** or **B**

Never both.

<!--
Strategy pattern via dependency injection. For mutually exclusive behavior. Exactly one implementation wins.
-->

---
layout: default
---

# Strategy: The Problem

Grid persists column state. But **where**?

| Context | Storage |
|---------|---------|
| Admin | Server |
| Public | localStorage |
| Preview | Don't save |

<!--
Three different behaviors. The grid shouldn't know which one.
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
Define the contract. Save and load. The token lets Angular swap implementations.
-->

---
layout: default
---

# Strategy: The Provider

```typescript
@Component({
  providers: [
    { provide: STORAGE_STRATEGY, useClass: ServerStorageStrategy }
  ]
})
export class AdminDashboard {}
```

**Zero if-statements.** Context decides, not component.

<!--
The decision lives in the provider. Same grid component, different storage behavior.
-->

---
layout: default
---

# The Cue

```typescript
export class DataGridComponent {
  #storage = inject(STORAGE_STRATEGY);
  #sorter = inject(SORT_STRATEGY);
  #filter = inject(FILTER_STRATEGY);
  #selection = inject(SELECTION_STRATEGY);
}
```

Can't opt out of any of these.

<!--
God component. Injecting four strategies. Six. Ten. Even with providers, you can't opt out.

The component owns behaviors that should be optional.
-->

---
layout: section
---

# Tool 4: Directives

**A** and **B** and **C**

Behaviors accumulate.

<!--
Directive composition. Each behavior is a directive. They don't replace each other - they stack.
-->

---
layout: default
---

# Directives: The Code

```typescript
@Directive({ selector: 'app-data-grid[sortable]' })
export class SortableDirective {
  #grid = inject(DataGridComponent);
}

@Directive({ selector: 'app-data-grid[persistColumns]' })
export class PersistColumnsDirective {
  #storage = inject(STORAGE_STRATEGY);
}
```

<!--
Each directive owns one behavior. The directive can use a strategy internally.
-->

---
layout: default
---

# Directives: Usage

```html
<!-- Simple -->
<app-data-grid sortable [data]="items" />

<!-- Full-featured -->
<app-data-grid sortable filterable persistColumns [data]="items" />
```

Compose what you need. Skip what you don't.

<!--
Simple view gets one directive. Power user view gets four. Same component.
-->

---
layout: default
---

# The Sign

```html
<!-- Page A, B, C... -->
<app-data-grid sortable filterable persistColumns contextMenu />
<app-data-grid sortable filterable persistColumns contextMenu />
<app-data-grid sortable filterable persistColumns contextMenu />
```

Same combo. Three times.

<!--
Same four directives. Copy-pasted. That's implicit coupling through repetition.
-->

---
layout: section
---

# The Promotion Rule

Two times is coincidence.

**Three times is a concept.**

## Name it.

<!--
When you see the same pattern three times, the code is telling you something's missing from your vocabulary.
-->

---
layout: default
---

# hostDirectives

```typescript
@Directive({
  selector: '[powerGrid]',
  hostDirectives: [
    SortableDirective,
    FilterableDirective,
    PersistColumnsDirective,
  ],
})
export class PowerGridDirective {}
```

One attribute instead of four.

<!--
Bundle directives into a named concept. Now you can talk about it, test it, document it.
-->

---
layout: default
---

# Coordinator Directive

```typescript
@Directive({
  selector: '[persistedSort]',
  hostDirectives: [SortableDirective, PersistColumnsDirective],
})
export class PersistedSortDirective {
  constructor() {
    effect(() => this.#persist.save('sort', this.#sortable.currentSort()));
  }
}
```

When A and B **must** work together.

<!--
Sometimes directives need to be coupled - by product spec. Create a coordinator. The grid stays dumb. The coupling is explicit.
-->

---
layout: default
---

# Decision Framework

| Signal | Tool |
|--------|------|
| Parent configures child | Inputs/Outputs |
| Boolean flags | Time to graduate |
| Structure varies | Content Projection |
| A or B | Strategy via DI |
| A and B and C | Directives |
| Same combo 3x | hostDirectives |

<!--
What is the code telling you right now? Each row is a response to a signal.
-->

---
layout: section
---

# The Takeaway

Good abstractions aren't chosen.

## They're discovered.

<!--
Our job is to listen to the code. Notice the repetition. Recognize the patterns.

Good abstractions aren't chosen. They're discovered.
-->

---
layout: end
---

# Thank You

**Dor Peled** · @Knat-Dev

Questions?

<!--
Come find me if you wanna talk about Angular, decoupling, or why your component has 47 inputs.
-->
