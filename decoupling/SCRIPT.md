# Decoupling in Angular: Letting the Code Tell You What It Needs

## Full Talk Script (Final Version v4)

---

### Slide 1 – About Me

**On screen:**

```
Dor Peled
@Knat-Dev

Software Engineer @ Coralogix
4 years in the industry
I love metal and code 🤘
```

**You say:**

"Hey everyone! I'm Dor, I go by Knat-Dev online. I'm a software engineer at Coralogix, been in the industry for about four years now.

Two things I love: metal and code. Sometimes at the same time.

Today I want to share a story about a refactor that taught me how to listen to code."

---

### Slide 2 – The Setup

**On screen:**

```
Month 1:
  rowData
  loading

That's it. That's the component.
```

**You say:**

"Six months ago, we built a data grid from scratch. Clean slate. No legacy.

Month one: two inputs. Row data. Loading state.

Clear responsibility. Easy to test. Life was good."

---

### Slide 3 – The Accumulation

**On screen:**

```
Month 6:
  rowData
  loading
  isAggregation
  onRowSelected
  onAggregationRowSelected
  ...and 5 more
```

**You say:**

"Month six.

Every feature added another input. Every consumer variation added another flag.

Look at those outputs - `onRowSelected` AND `onAggregationRowSelected`. Two outputs for what should be one concept.

We didn't inherit this mess. We built it. One reasonable decision at a time."

---

### Slide 4 – The Breaking Point

**On screen:**

```
"Can we reuse this grid
 in two other places?"
```

**You say:**

"Then product asked the question.

'Can we reuse this grid in two other places? Same core, slightly different behaviors.'

And we discovered the component was welded shut."

---

**[MEME: "This is fine" dog in burning room]**

---

**You say:**

"Every variation required threading another flag through the entire stack.

This talk is about how we took it apart - and the mental model I now use to see these signals before the deadline hits."

---

### Slide 5 – The Real Enemy

**On screen:**

```
Coupling isn't the problem.

Implicit coupling is.
```

**You say:**

"Every system has coupling. That's not the issue.

The issue is whether that coupling is visible and intentional - or scattered and accidental.

Today I'll show you four decoupling tools Angular gives us. When each one fits. And the signal that tells you it's time for the next one."

---

### Slide 6 – Let's Pair

**On screen:**

```
Imagine we're pairing on this.

You and me.
Looking at the same screen.
```

**You say:**

"I want you to imagine something.

We're sitting together. Pairing on this grid component. You're driving, I'm navigating.

We're looking at the code, and I'm pointing at things saying 'see that? That's a signal.'

Let's go."

---

### Slide 7 – Tool 1: Inputs/Outputs

**On screen:**

```
The Baseline
```

**You say:**

"Inputs and outputs. The baseline. Everyone's first tool.

Parent decides what. Child decides how.

This is correct. This is where you start."

---

### Slide 8 – Inputs/Outputs: The Code

**On screen:**

```typescript
@Component({
  selector: 'app-data-grid',
})
export class DataGridComponent {
  readonly rowData = input<Row[]>([]);
  readonly loading = input(false);
}
```

**You say:**

"Month one. Two inputs. Clean contract.

This is the right abstraction for this moment."

---

### Slide 9 – Inputs/Outputs: The Signal

**On screen:**

```typescript
// The signal it's breaking down:
readonly isAggregation = input(false);

handleRowClick(row: Row) {
  if (this.isAggregation()) {
    this.aggregationSelected.emit(row);
    return;
  }
  this.rowSelected.emit(row);
}
```

**You say:**

"Here's the signal.

The component is making decisions based on who's using it. It knows too much about its consumers.

When you see `if` branches based on context flags - inputs have outgrown their welcome."

---

### Slide 10 – Tool 2: Content Projection

**On screen:**

```
When structure varies
```

**You say:**

"Content projection. The parent passes structure in, not configuration down.

Use this when the variation is about what goes where - not about behavior."

---

### Slide 11 – Content Projection: The Code

**On screen:**

```typescript
@Component({
  selector: 'app-panel',
  template: `
    <div class="header">
      <ng-content select="[header]" />
    </div>
    <div class="body">
      <ng-content />
    </div>
  `,
})
export class PanelComponent {}
```

```html
<app-panel>
  <h2 header>My Title</h2>
  <p>My content here</p>
</app-panel>
```

**You say:**

"The panel owns layout. The consumer owns meaning.

They evolve independently. Beautiful - when structure is what varies."

---

### Slide 12 – Content Projection: The Signal

**On screen:**

```
Works for: header, footer, actions

Doesn't work for:
  "click this row differently"
  "fetch data this way"
  "navigate on enter"
```

**You say:**

"The signal it's breaking down?

When you need different behavior, not different structure.

Content projection can't help you there."

---

### Slide 13 – Tool 3: Strategy via DI

**On screen:**

```
When behavior is exclusive

A or B. Never both.
```

**You say:**

"Strategy via dependency injection.

This is for mutually exclusive behavior. Exactly one wins at runtime.

REST or WebSocket. Pagination or streaming. Log detail or aggregation drilldown."

---

### Slide 14 – Strategy: The Code

**On screen:**

```typescript
export interface RowClickHandler {
  handle(row: Row): void;
}

export const ROW_CLICK_HANDLER =
  new InjectionToken<RowClickHandler>('RowClickHandler');
```

```typescript
// Context A:
providers: [
  {
    provide: ROW_CLICK_HANDLER,
    useClass: LogDetailHandler,
  },
];

// Context B:
providers: [
  {
    provide: ROW_CLICK_HANDLER,
    useClass: AggregationHandler,
  },
];
```

**You say:**

"The grid no longer knows which context it's in.

It just calls the handler. The decision lives in the provider, where it belongs."

---

### Slide 15 – Strategy: The Grid Now

**On screen:**

```typescript
export class DataGridComponent {
  private handler = inject(ROW_CLICK_HANDLER);

  handleRowClick(row: Row) {
    this.handler.handle(row);
  }
}
```

**You say:**

"No more `if (isAggregation)`.

One line. The component is ignorant of its context. That's the goal."

---

### Slide 16 – Strategy: The Signal

**On screen:**

```typescript
@if (featureA() && featureB()) {
  <row-variant-ab />
}
@if (featureA() && !featureB()) {
  <row-variant-a />
}
```

**You say:**

"Here's the signal strategy is breaking down.

The moment you need 'this AND that' - not 'this OR that' - strategy bends.

You're not choosing behavior anymore. You're accumulating it."

---

### Slide 17 – The Combinatorial Problem

**On screen:**

```
2 features → 4 cases
3 features → 8 cases
4 features → 16 cases
```

**You say:**

"This is a combinatorial smell.

The cases grow faster than the features. The template becomes the integration layer.

And templates are terrible integration layers."

---

**[MEME: "One does not simply handle every feature combination with @if blocks"]**

---

### Slide 18 – Tool 4: Directive Composition

**On screen:**

```
When behaviors accumulate

This AND that AND that.
```

**You say:**

"Directive composition.

Each behavior is a directive. They coexist on the same element. They compose by addition."

---

### Slide 19 – Directives: The Code

**On screen:**

```typescript
@Directive({ selector: '[selectableRow]' })
export class SelectableRowDirective {
  readonly isSelected = signal(false);
  toggle() {
    this.isSelected.update((v) => !v);
  }
}

@Directive({ selector: '[keyboardNav]' })
export class KeyboardNavDirective {
  // handles arrow keys, enter, etc.
}

@Directive({ selector: '[hoverHighlight]' })
export class HoverHighlightDirective {
  // handles hover state
}
```

**You say:**

"Each directive owns one reason to change.

Selection logic? One file. Keyboard navigation? Different file. Hover styling? Another file.

They don't know about each other."

---

### Slide 20 – Directives: The Usage

**On screen:**

```html
<tr selectableRow keyboardNav hoverHighlight></tr>
```

**You say:**

"They compose by coexisting.

If you've seen ECS in game engines - same idea. The element is the entity. Directives are components. Behaviors as data you attach."

---

### Slide 21 – Directives: The Noise

**On screen:**

```html
<tr
  selectableRow
  keyboardNav
  hoverHighlight
  copyOnClick
  contextMenu
></tr>
```

**You say:**

"This is where people get uncomfortable.

The behavior is decoupled. Each directive does one thing. But the usage is noisy."

---

**[MEME: Distracted boyfriend looking at "clean architecture" while ignoring "readable templates"]**

---

### Slide 22 – The Real Problem

**On screen:**

```html
<!-- file-a.html -->
<tr selectableRow keyboardNav hoverHighlight></tr>

<!-- file-b.html -->
<tr selectableRow keyboardNav hoverHighlight></tr>

<!-- file-c.html -->
<tr selectableRow keyboardNav hoverHighlight></tr>
```

**You say:**

"Here's the real problem.

If this same combination appears in three places - that's implicit coupling through repetition.

Same directives. Same order. Same assumptions. Scattered."

---

### Slide 23 – The Promotion Rule

**On screen:**

```
Two times is coincidence.

Three times is a concept.

Name it.
```

**You say:**

"This is the core insight.

Two times is coincidence. Three times is a concept.

When you see the same pattern three times - the code is telling you something is missing from your model. Something that deserves a name."

---

### Slide 24 – Promotion: The Code

**On screen:**

```typescript
@Directive({
  selector: '[interactiveRow]',
  hostDirectives: [
    SelectableRowDirective,
    KeyboardNavDirective,
    HoverHighlightDirective,
  ],
})
export class InteractiveRowDirective {}
```

**You say:**

"Host directives let you compose directives into a named concept.

This isn't abstraction up front. This is abstraction earned through repetition."

---

### Slide 25 – Promotion: Before/After

**On screen:**

```html
<!-- Before: implicit pattern -->
<tr selectableRow keyboardNav hoverHighlight></tr>

<!-- After: explicit concept -->
<tr interactiveRow></tr>
```

**You say:**

"Promotion isn't about reducing characters.

It's about making implicit concepts explicit. Now you can talk about it. Test it. Evolve it in one place."

---

**[MEME: "Friendship ended with scattered directives, now interactiveRow is my best friend"]**

---

### Slide 26 – When Directives Need to Talk

**On screen:**

```
"When selected, enable keyboard nav"
"When hovered, change highlight priority"

Coordination is coupling.
Where does it live?
```

**You say:**

"Sometimes these directives need to interact.

Selection affects keyboard navigation. Hover affects styling priority.

That interaction is coupling. It's unavoidable. The question is where it lives."

---

### Slide 27 – Coordination: The Code

**On screen:**

```typescript
@Directive({
  selector: '[interactiveRow]',
  hostDirectives: [
    SelectableRowDirective,
    KeyboardNavDirective,
  ],
})
export class InteractiveRowDirective {
  private selectable = inject(SelectableRowDirective, {
    self: true,
  });
  private keyboard = inject(KeyboardNavDirective, {
    self: true,
  });

  constructor() {
    effect(() => {
      if (this.selectable.isSelected()) {
        this.keyboard.enable();
      }
    });
  }
}
```

**You say:**

"The promoted directive becomes the coordinator.

The coupling exists - but now it's explicit, testable, isolated. One place with a name."

---

### Slide 28 – The Mediator Pattern

**On screen:**

```
This is the Mediator pattern.

Coupling still exists.
It just has an address now.
```

**You say:**

"This is essentially the Mediator pattern from Gang of Four.

The coupling didn't disappear. It just has an address now."

---

**[MEME: Pam from The Office - "Corporate needs you to find the difference between coupling scattered everywhere and coupling in one named place" - "They're actually very different"]**

---

### Slide 29 – One Rule

**On screen:**

```
The coordinator coordinates
its hosted directives.

Nothing else.
```

**You say:**

"One rule.

The coordinator only coordinates its hosted directives. The moment it reaches into services, app state, router - it's no longer a coordinator.

It's a god object. Don't do that."

---

### Slide 30 – The Decision Framework

**On screen:**

| Signal                       | Tool                     |
| ---------------------------- | ------------------------ |
| Parent configures child      | Inputs/Outputs           |
| Context flags, `if` branches | → Time to graduate       |
| Structure varies             | Content Projection       |
| Behavior exclusive (A or B)  | Strategy via DI          |
| Behaviors accumulate         | Directives               |
| Same combo repeats 3x        | Promote (hostDirectives) |
| Composed behaviors interact  | Coordinator              |

**You say:**

"This is the mental model.

Not 'which pattern is best' - but 'what is the code telling me right now?'

Each row is a response to a signal. You don't pick up front. You listen."

---

### Slide 31 – Your Call to Action

**On screen:**

```
Monday morning:

Look at your biggest component.
Count the inputs.
Find the if-branches.

Ask: "What signal is this?"
```

**You say:**

"Here's what I want you to do Monday morning.

Find your biggest component. Count the inputs. Find the `if` branches that check context.

Ask yourself: what signal is this giving me? What tool does it point to?

You might not refactor it. But you'll see it differently."

---

### Slide 32 – The Takeaway

**On screen:**

```
Good abstractions aren't chosen.

They're discovered.
```

**You say:**

"Angular gives us the tools. Inputs. Content projection. DI. Directives. Host directives.

Our job isn't to pick our favorite and apply it everywhere.

Our job is to listen to the code. Notice the repetition. Recognize the signals.

Good abstractions aren't chosen. They're discovered."

---

_Pause._

---

### Slide 33 – Thank You

**On screen:**

```
Dor Peled
@Knat-Dev

Thanks for listening 🤘

Questions?
```

**You say:**

"Thank you.

I'm Dor, Knat-Dev online. Come find me if you want to talk about decoupling, Angular, or metal.

Questions?"

---

## Timing Estimate

| Slide     | Content                    | Minutes     |
| --------- | -------------------------- | ----------- |
| 1         | About me                   | 0.5         |
| 2-4       | The story setup            | 2           |
| 5-6       | Real enemy + pairing setup | 1.5         |
| 7-9       | Inputs/Outputs             | 2           |
| 10-12     | Content Projection         | 1.5         |
| 13-17     | Strategy via DI            | 2.5         |
| 18-22     | Directive composition      | 2.5         |
| 23-25     | Promotion rule             | 2.5         |
| 26-29     | Coordination               | 2.5         |
| 30        | Decision framework         | 1.5         |
| 31-33     | CTA + Close                | 1.5         |
| **Total** |                            | **~20 min** |

---

## Q&A Prep

**"Why not just start with directives?"**

> "Because inputs are simpler, and simpler is better when sufficient. You don't reach for directives until you feel the pain. Premature abstraction is its own coupling."

**"What's the signal inputs have broken down?"**

> "When your component has `if` branches based on _who's consuming it_. When you're adding paired outputs like `rowSelected` and `aggregationRowSelected`."

**"When do I promote?"**

> "Two times is coincidence. Three times is a concept. Name it."

**"How do I prevent the coordinator becoming a god object?"**

> "It only coordinates its hosted directives. If it reaches into services or app state, it's doing too much."

**"What does `{ self: true }` do?"**

> "Ensures you inject the directive on _this_ element, not one from a parent. Makes intent explicit."

**"Can I expose inputs from host directives?"**

> "Yes. `hostDirectives` supports input/output mapping. I kept slides simple but the capability is there."

---

## Change Log from v3

| Item         | Change                                           |
| ------------ | ------------------------------------------------ |
| Added        | About me slides (open and close)                 |
| Added        | "Let's pair" imagination framing (Slide 6)       |
| Added        | Call to action slide (Slide 31)                  |
| Removed      | AI coda entirely                                 |
| Simplified   | Code examples - fewer lines per slide            |
| Split        | Dense slides into multiple focused slides        |
| Added        | Clear "signal" framing for each tool's breakdown |
| Restructured | Flow follows Shai's "why → how → do" arc         |

---

Ready for rehearsal or slide design?
