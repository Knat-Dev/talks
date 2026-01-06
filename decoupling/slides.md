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
Hey everyone, I'm Dor. I'm a software engineer at Coralogix.

Today I want to share something that took me a while to figure out. Not a new pattern. Not a library. More like... a way of listening to your code.

We all know the tools - inputs, outputs, content projection, dependency injection, directives. But knowing WHAT they are isn't the hard part. The hard part is knowing WHEN to reach for each one.

So this talk is about the signals. The moments when your code is quietly telling you it needs something different. And what to do when you notice.
-->

---
layout: section
---

# About Me

**Dor Peled** · @Knat-Dev

Software Engineer @ Coralogix

_I tend to stay with hard things longer than is comfortable_

<!--
Quick bit about me before we dive in.

I've noticed something about myself over the years. When something gets complicated or messy, I usually don't bounce right away. I tend to sit with it longer than is comfortable, trying to understand what's actually going on.

That shows up in different places. In reading, I gravitate toward long, dense series like Malazan Book of the Fallen. Ten books. Three and a half million words. Characters you forget and have to look up. And that same instinct shows up in code.

Large Angular codebases behave the same way. They don't fail immediately. They don't throw errors on day one. They accumulate complexity quietly, one feature at a time. And if you don't stay with them long enough, you miss the signals.

The signals that tell you: "Hey, this component is under pressure. It needs a different shape."

So this talk isn't a list of best practices. It's not "always do X" or "never do Y." It's about what I learned by staying with components that were struggling. By noticing what the code was trying to tell me.
-->

---
layout: default
---

# Month 1

```typescript
@Component({ selector: 'app-data-grid' })
export class DataGridComponent<T> {
  data = input<T[]>([]);
  columns = input<ColumnDef<T>[]>([]);
  loading = input(false);
}
```

Clean. Simple. Works great.

<!--
Let me tell you a story. It's one you've probably lived.

Month one. You're building a data grid. Maybe it's for an admin panel. Maybe it's for analytics. Doesn't matter. You write a component with three inputs - data, columns, loading state. Clean. Simple. Easy to test.

You look at it and think: "This is good code. I'm proud of this."

And you should be. At this moment, this is exactly the right abstraction. Nothing is over-engineered. Nothing is premature. The component does one thing and does it well.

This is where you want to stay as long as possible. Enjoy it. Because it won't last.
-->

---
layout: default
---

# Month 6

```typescript
@Component({ selector: 'app-data-grid' })
export class DataGridComponent<T> {
  data = input<T[]>([]);
  columns = input<ColumnDef<T>[]>([]);
  loading = input(false);
  sortable = input(false);
  filterable = input(false);
  persistColumns = input(false);
  // ... and it keeps growing
}
```

<v-click>
<img src="/assets/this-is-fine.jpg" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-120 rounded-lg shadow-xl" />
</v-click>

<!--
Month six. Product comes to you. "Hey, can we add sorting?" Sure. "Can users resize columns?" Yeah, okay. "Can we remember their column preferences?" ...Fine.

And then: "Can we have a compact mode for the mobile view? Oh, and admins need a delete button but regular users shouldn't see it."

And suddenly you look at this component and realize... it's stuck. Every change you make breaks something else. The tests are fragile. The template is a maze of ngIfs. You're afraid to touch it.

Here's the thing that took me a while to accept: We didn't inherit this mess. We built it. One reasonable feature request at a time. Each decision made sense in isolation. But together? They created something that fights back.

[click for meme]

This is fine, right? We've all been here. Sitting in the flames, pretending everything is okay.
-->

---
layout: section
---

# The Real Problem

Coupling isn't bad.

## Hidden coupling is.

<!--
So what went wrong?

It's tempting to say "coupling is bad" and leave it there. But that's not quite right. Every system has coupling. Your components HAVE to talk to each other somehow. Data has to flow. Events have to propagate.

Coupling isn't the enemy. HIDDEN coupling is.

When you look at a component and you can see its dependencies - in the constructor, in the inputs, in the imports - you can reason about it. You can manage it. You can decide if it's worth the tradeoff.

But when coupling is hidden - when it's buried in boolean flags, or scattered across templates, or implicit in the way things are used - that's when you lose control. You make a change over here, and something breaks over there, and you have no idea why.

So the question isn't "how do I remove coupling?" It's "how do I make coupling visible and intentional?"

That's what the next four tools are about.
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
Here's the mental model I want to share with you. Four tools. You already know all of them. But the trick isn't knowing what they are - it's recognizing WHEN each one is the right choice.

Let me walk through this table quickly, and then we'll go deep on each one.

Inputs and outputs - the default. Parent passes data down, child emits events up. This is where you start, and where you should stay as long as possible.

Content projection - when the STRUCTURE varies. Not the behavior, the structure. When different consumers need to put different things in different slots.

Strategy via DI - when you have mutually exclusive behaviors. A OR B. Never both at the same time. The admin saves to the server OR the public user saves to localStorage. One or the other.

Directives - when behaviors accumulate. A AND B AND C. Sorting AND filtering AND column persistence. You want all of them, or some of them, mixed and matched.

Each tool responds to a different signal from your code. Let's look at them one by one.
-->

---
layout: section
---

# Tool 1: Inputs/Outputs

**Parent** decides WHAT

**Child** decides HOW

<!--
Tool number one. The one everyone learns first. And honestly? The one you should use as long as you possibly can.

The mental model is simple. The parent decides WHAT - what data to show, what configuration to use. The child decides HOW - how to render it, how to handle interactions.

This separation is powerful. The child component doesn't need to know where the data came from. It doesn't care if it's from an API, from a store, from a mock. It just receives data and emits events.

And here's the key insight: Inputs and outputs create VISIBLE coupling. When you look at a component's API, you can see exactly what it depends on. That's a good thing. That's manageable coupling.

The danger comes when you start adding inputs that aren't about WHAT to show, but about WHO is using the component. That's when you've outgrown this tool.
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

Component asking _"who's using me?"_

<!--
Here's the smell. This is the signal that tells you inputs have reached their limit.

Look at these inputs. isAdmin. isCompactMode. These aren't data inputs. These aren't "what should I display." These are context flags. The component is essentially asking: "Who's using me? What page am I on?"

And then it changes its behavior based on the answer. If admin, show the delete button. If compact mode, hide some columns.

This is hidden coupling. The component now has implicit knowledge about its consumers. It knows there's an admin context. It knows there's a compact mode. Every time you add a new context, you have to modify this component.

The code is telling you something: "I don't want to know who's using me. I want to be told what to do."

The moment a component asks who you are instead of what you need, it has crossed a boundary.

When you see if-branches based on context flags - isAdmin, isReadOnly, isEmbedded, isCompact - that's your signal. Inputs have outgrown their welcome. Time to look at other tools.
-->

---
layout: section
---

# Tool 2: Content Projection

When **structure** varies

Not behavior. Structure.

<!--
Tool number two. Content projection. ng-content.

This is for when the variation isn't about behavior - it's about STRUCTURE. What goes where. What elements appear in which slots.

The key mental shift here is that instead of the parent passing configuration DOWN to the child, the parent passes structure IN. The child says "I have these slots" and the parent says "I'll fill them with these elements."

Think of it like a picture frame. The frame decides the shape, the size, the border. But it doesn't decide what picture goes inside. That's the consumer's choice.

Use this when different consumers need different content in the same structural positions. A card where one page wants a chart in the header and another wants a title. A modal where the body could be a form or a confirmation message.

The component owns the layout. The consumer owns the content.
-->

---
layout: default
---

# Content Projection

```typescript
template: `
  <div class="header"><ng-content select="[header]" /></div>
  <ng-content />
`;
```

Card owns layout. Consumer owns content.

<!--
Here's what it looks like in practice.

The component template has two ng-content elements. One with a selector for header content, one for the default slot.

The component is saying: "I'll wrap your content in a div with a header class. I'll put your main content below. But what goes IN those slots? That's up to you."

Different pages can now use this card differently. One puts a title in the header. Another puts a toolbar with buttons. Another leaves the header empty entirely. The card doesn't care. It just provides the structure.

This is a really common pattern for layout components - cards, modals, panels, drawers. Anywhere the shell is consistent but the contents vary.
-->

---
layout: default
---

# The Tell

Doesn't work for:

- _"Save to localStorage vs server"_
- _"Sort ascending vs descending"_

You need different **behavior**, not structure.

<!--
But here's the tell. Here's how you know content projection ISN'T the right tool.

When someone says "save to localStorage vs save to the server" - that's not structure. You can't ng-content a storage mechanism.

When someone says "sort ascending vs descending" - that's not structure either. That's behavior.

Content projection is for elements. DOM nodes. Visual things. If you find yourself trying to project behavior, trying to pass functions through slots, you've gone too far. You need a different tool.

I've seen people try to make content projection do everything. "What if we project a template that contains the sorting logic?" Stop. That's not what this tool is for.

When the variation is about WHAT HAPPENS, not WHAT APPEARS, you need tool number three.
-->

---
layout: section
---

# Tool 3: Strategy via DI

**A** or **B**

Never both.

<!--
Tool number three. Strategy pattern, implemented through Angular's dependency injection.

The key phrase here is "A or B. Never both."

This is for mutually exclusive behaviors. Situations where exactly ONE implementation should be active at a time. You're either saving to the server OR to localStorage. You're either using the real API OR the mock. You're either in dark mode OR light mode.

The component shouldn't know which one. It shouldn't have if-statements checking context. It should just say "I need SOMETHING that can save data" and let the DI system provide the right implementation.

This is the strategy pattern. You define an interface - the contract. Then you create multiple implementations of that contract. And the context decides which implementation to inject.

The component stays dumb. The decision moves to the provider.
-->

---
layout: image-right
---

::left::

# Strategy: The Problem

Grid persists column state. But **where**?

::default::

| Context | Storage      |
| ------- | ------------ |
| Admin   | Server       |
| Public  | localStorage |
| Preview | Don't save   |

<!--
Let me give you a concrete example.

We have a data grid. It lets users customize their columns - reorder them, resize them, hide some. And we want to persist those preferences so they don't lose their setup when they refresh.

But WHERE we persist depends on context.

In the admin dashboard, we save to the server. The admin's preferences should sync across devices.

In the public-facing view, we save to localStorage. We don't have a user account to attach it to.

In the preview mode, we don't save at all. It's a temporary sandbox.

Now, the old way would be to add an input: storageMode = 'server' | 'local' | 'none'. And then have if-statements everywhere.

But that means the grid knows about all three modes. Every time you add a new one, you modify the grid. That's the hidden coupling we talked about.

The grid shouldn't know. It should just say "I need to persist this" and let someone else figure out where.
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
Here's how we implement it.

First, we define the interface. The contract. A StorageStrategy has two methods: save and load. That's it. We're not specifying HOW it saves. Just that it CAN save.

Then we create an InjectionToken. This is Angular's way of saying "there will be something that implements this interface, and I'll need to look it up by this token."

The interface is the what. The token is the lookup key. Neither of them contains any actual implementation.

This is the foundation. Once you have this, you can create as many implementations as you need, and the grid will never know the difference.
-->

---
layout: default
---

# Strategy: Implementations

```typescript
export class LocalStorageStrategy
  implements StorageStrategy
{
  save(key: string, data: unknown) {
    localStorage.setItem(key, JSON.stringify(data));
  }
}

export class ServerStorageStrategy
  implements StorageStrategy
{
  save(key: string, data: unknown) {
    this.http.post('/api/preferences', { key, data });
  }
}
```

<!--
Now we create the implementations.

LocalStorageStrategy - saves to the browser's localStorage. Simple. Synchronous. Works offline.

ServerStorageStrategy - posts to an API endpoint. The preferences live on the server. Syncs across devices.

Notice they both implement the same interface. Same method names. Same signatures. From the outside, they're interchangeable.

You could add a third one - NoopStorageStrategy - that does nothing. For preview mode. Or a SessionStorageStrategy that clears when you close the tab. Or an IndexedDbStrategy for large datasets.

The grid doesn't care. It just calls save() and load(). Whatever implementation is injected will handle the details.

This is the power of the strategy pattern. The behavior varies, but the interface stays stable.
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
And here's where the decision lives. In the providers array.

The AdminDashboard component says: "In my subtree, when someone asks for STORAGE_STRATEGY, give them ServerStorageStrategy."

That's it. The grid is somewhere inside this component's template. When it injects STORAGE_STRATEGY, it gets the server implementation. No if-statements. No configuration inputs. The context decided.

A different page - say, the public dashboard - would provide LocalStorageStrategy instead. Same grid component. Different behavior.

This is what I mean by "zero if-statements." The branching logic isn't in the component. It's in the providers. The coupling is explicit. You can see it. You can reason about it. You can test it.

And if product comes to you tomorrow and says "we need a fourth mode," you don't touch the grid at all. You write a new strategy class and provide it in the new context.
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

<v-click>
<img src="/assets/one-does-not-simply.jpg" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-120 rounded-lg shadow-xl" />
</v-click>

<!--
But here's where the strategy pattern hits its limit. Here's the cue that tells you it's time for something else.

Look at this component. It's injecting four strategies. Storage, sorting, filtering, selection. Maybe six. Maybe ten.

Even though each strategy can be swapped out... you can't OPT OUT. Every consumer of this grid gets all four behaviors. You can change HOW sorting works, but you can't say "I don't want sorting at all."

The grid has become a god component. It owns every possible behavior. Even if those behaviors are abstracted behind interfaces, they're still mandatory.

[click for meme]

One does not simply opt out of a required dependency.

If you're injecting five strategies and half of them should be optional, you're using the strategy pattern for something it was never meant to solve.

When you see a component injecting many strategies, and some of those behaviors should be OPTIONAL, that's your signal. You need behaviors that accumulate, not behaviors that are always present.

That's tool number four.
-->

---
layout: section
---

# Tool 4: Directives

**A** and **B** and **C**

Behaviors accumulate.

<!--
Tool number four. Directive composition.

The key phrase here is "A AND B AND C." Behaviors accumulate. They stack on top of each other.

With strategies, you pick ONE implementation. With directives, you can have MANY behaviors active at the same time. Sorting AND filtering AND persistence AND context menus. Or just sorting. Or just persistence. Whatever combination you need.

Each behavior lives in its own directive. The directives don't replace each other - they attach to the same host element and each one adds its own functionality.

The component stays minimal. It provides the foundation - the data, the rendering, the core interaction. The directives add optional superpowers.

This is composition over configuration. Instead of configuring one big thing, you compose many small things.
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
Here's what it looks like in code.

SortableDirective. Its selector is 'app-data-grid[sortable]'. That means it only activates when you put the sortable attribute on a data grid. It injects the DataGridComponent so it can interact with the grid's internals.

PersistColumnsDirective. Same pattern. It only activates when you add persistColumns to the grid. And notice - it injects STORAGE_STRATEGY. So you can still use the strategy pattern INSIDE a directive. The directive decides WHEN persistence happens. The strategy decides WHERE it persists.

These tools aren't mutually exclusive. They layer on top of each other. You might use inputs for basic configuration, a strategy for swappable behavior, and directives for optional features.

Each directive owns exactly one responsibility. Single-purpose. Easy to test. Easy to understand.
-->

---
layout: default
---

# Directives: Usage

```html
<!-- Simple -->
<app-data-grid sortable [data]="items" />

<!-- Full-featured -->
<app-data-grid
  sortable
  filterable
  persistColumns
  [data]="items"
/>
```

Compose what you need. Skip what you don't.

<!--
And here's what usage looks like in the template.

Simple case - you just want sorting. One directive. The grid renders data, the sortable directive adds click handlers to column headers and manages sort state.

Full-featured case - you want everything. Sorting, filtering, column persistence. Same grid component. Four directives attached to it.

The beautiful thing is that each page can choose its own combination. The read-only view might just have the grid, no directives at all. The power user view has everything. The embedded widget has sorting but not filtering.

The grid doesn't know. It doesn't care. It just renders data. The directives add behavior when they're present.

This is opt-in complexity. You start simple, and you add what you need.
-->

---
layout: default
---

# The Sign

```html
<!-- Page A, B, C... -->
<app-data-grid
  sortable
  filterable
  persistColumns
  contextMenu
/>
<app-data-grid
  sortable
  filterable
  persistColumns
  contextMenu
/>
<app-data-grid
  sortable
  filterable
  persistColumns
  contextMenu
/>
```

Same combo. Three times.

<v-click>
<img src="/assets/distracted-boyfriend.jpg" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-120 rounded-lg shadow-xl" />
</v-click>

<!--
But here's the sign that tells you directives alone aren't enough.

Look at this. Three different pages. Same four directives. Copy-pasted. sortable filterable persistColumns contextMenu. Over and over.

This is a different kind of coupling. It's not hidden in if-statements. It's hidden in REPETITION. Every time someone creates a new page with a data grid, they have to remember: "Oh right, we always use these four together."

And what happens when you need to add a fifth directive to the standard combo? You have to find every place it's used and update them all. What if you miss one?

[click for meme]

The boyfriend is looking at this nice clean abstraction, ignoring the girlfriend who represents the actual pattern in your codebase.

When you see the same combination of directives three times, that's not coincidence. That's a concept that doesn't have a name yet.
-->

---
layout: section
---

# The Promotion Rule

Two times is coincidence.

**Three times is a concept.**

## Name it.

<!--
This is my favorite rule. I call it the promotion rule.

Once is just code. You wrote something. It works. Great.

Twice is coincidence. Maybe two places happen to need the same thing. It's not necessarily a pattern.

But three times? Three times is a concept. The code is telling you: "This thing you keep writing? It has a shape. It has meaning. Give it a name."

When you name something, you can talk about it. You can document it. You can test it as a unit. You can evolve it in one place.

Unnamed patterns are dangerous. They drift apart over time. One developer updates one instance but not the others. Now you have three slightly different versions of the same thing.

I've seen this play out. Before we named our "power grid" pattern, one instance had filtering disabled "temporarily" for a demo. Six months later, nobody remembered why. It just... stayed that way.

So when you see a pattern three times: name it. Promote it to a first-class concept in your codebase.
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
And Angular gives us a beautiful way to do this: hostDirectives.

Look at this. PowerGridDirective. One directive that bundles three others together. When you put powerGrid on an element, you automatically get SortableDirective, FilterableDirective, and PersistColumnsDirective.

The individual directives still exist. You can still use them separately when you need fine-grained control. But now you also have a named concept - "power grid" - that represents the standard combination.

The directive itself has no logic. It's empty. Its only job is to compose other directives together. It's a name for a pattern.

This is how you turn implicit coupling - "we always use these together" - into explicit coupling - "these belong together, here's the proof."

Now when someone needs to add a fifth behavior to all power grids, they update one file. One place. Done.
-->

---
layout: default
---

# Before/After

```html
<!-- Before: 4 attributes -->
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

<v-click>
<img src="/assets/pam-theyre-different.jpg" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-120 rounded-lg shadow-xl" />
</v-click>

<!--
Before and after.

Before: four attributes. sortable, filterable, persistColumns, contextMenu. You have to remember them all. You have to type them all. You have to keep them in sync across your codebase.

After: one attribute. powerGrid. One word that means "I want the full-featured experience."

[click for meme]

They're the same picture. Functionally identical. But conceptually? Worlds apart.

The second version communicates intent. When a new developer reads the template, they see "powerGrid" and they know: "Ah, this is a power grid. I know what that means in this codebase."

The first version is just... a list of things. No cohesion. No name. No concept to hold onto.

Naming matters. It's not just about saving keystrokes. It's about building a shared vocabulary for your team.
-->

---
layout: default
---

# Coordinator Directive

```typescript
@Directive({
  selector: '[persistedSort]',
  hostDirectives: [
    SortableDirective,
    PersistColumnsDirective,
  ],
})
export class PersistedSortDirective {
  constructor() {
    effect(() =>
      this.#persist.save(
        'sort',
        this.#sortable.currentSort()
      )
    );
  }
}
```

When A and B **must** work together.

<v-click>
<img src="/assets/galaxy-brain.jpg" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-120 rounded-lg shadow-xl" />
</v-click>

<!--
One more pattern. The coordinator directive.

Sometimes two behaviors MUST work together. Not just "we usually use them together" but "the product spec says when you sort, we have to persist the sort state."

That's not coincidental coupling. That's intentional coupling. And we should make it explicit.

PersistedSortDirective. It uses hostDirectives to bring in both SortableDirective and PersistColumnsDirective. But then it adds coordination logic - an effect that watches the sort state and saves it whenever it changes.

The grid still doesn't know about persistence. The sortable directive doesn't know about persistence. But THIS directive - the coordinator - knows about both and makes them work together.

[click for meme]

Galaxy brain moment. The coupling that product requires... now lives in one place. Named. Testable. Documented.

If the spec changes - if they decide persisted sorting should work differently - you change one file. Not the grid. Not the sortable directive. Just the coordinator.

This is how you handle INTENTIONAL coupling. You don't hide it. You give it a home.
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
Let's bring it all together. Here's your decision framework.

What is the code telling you right now?

Parent needs to configure child? Inputs and outputs. Stay here as long as possible.

You're adding boolean flags like isAdmin or isCompactMode? That's the smell. Time to graduate to something else.

The variation is about structure - what goes where, what elements appear? Content projection.

The variation is about behavior, and it's mutually exclusive - A OR B, never both? Strategy via DI.

The variation is about optional behaviors that stack - A AND B AND C? Directives.

You see the same combination of directives three times? Bundle them with hostDirectives. Name the pattern.

Each row is a response to a signal. The code tells you what it needs. Your job is to listen.
-->

---
layout: section
---

# The Takeaway

Good abstractions aren't chosen.

## They're discovered.

<!--
I want to leave you with this thought.

We often talk about "choosing the right abstraction." Like there's a menu and you pick from it at the start of a project.

But that's not how it works. Good abstractions aren't chosen. They're discovered.

You write code. You watch it evolve. You notice where it's struggling. And the code tells you - through boolean flags, through repetition, through god components - what it actually needs.

They're discovered when booleans pile up, when directives repeat, when components start fighting you.

Your job isn't to predict the future. It's to listen to the present. To notice the signals. To respond when the code says "I'm ready for a different shape."

Stay with your components long enough to hear what they're saying. The patterns will reveal themselves.
-->

---
layout: end
---

# Thank You

**Dor Peled** · @Knat-Dev

Software Engineer @ Coralogix

Questions?

<!--
That's it. Thank you for listening.

If any of this resonated - if you've got a component right now that's fighting back - come find me. I'd love to hear about it.

Or if you just want to talk about Angular, decoupling, Malazan Book of the Fallen, or why your component has 47 inputs... I'm around.

Questions?
-->
