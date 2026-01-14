# Q&A Cheat Sheet — Decoupling in Angular

Quick answers for anticipated questions after the talk.

---

## Content Projection

### Q: What if projected content needs to know about a directive (e.g., header needs sort button)?

Use `exportAs` to expose the directive to the template:

```ts
@Directive({
  selector: 'app-list[sortable]',
  exportAs: 'sortable'
})
export class Sortable {
  sortDir = signal<'asc' | 'desc'>('asc');

  toggle() {
    this.sortDir.update(d => d === 'asc' ? 'desc' : 'asc');
  }
}
```

```html
<app-list sortable #sort="sortable" [items]="data">
  <app-list-header header>
    Items
    @if (sort) {
      <button (click)="sort.toggle()">{{ sort.sortDir() === 'asc' ? '↑' : '↓' }}</button>
    }
  </app-list-header>
</app-list>
```

**Alternative:** The projected component injects the directive via DI:

```ts
@Component({ selector: 'app-list-header' })
export class ListHeader {
  sortable = inject(Sortable, { optional: true });
}
```

### Q: When should content projection NOT be used?

When you need **behavior**, not structure. `ng-content` is for DOM elements only — you can't project services or logic.

---

## Strategy via DI

### Q: What if I need a default/fallback when no strategy is provided?

Use a `NoopStrategy` at the root:

```ts
// app.config.ts
providers: [
  { provide: STORAGE_STRATEGY, useClass: NoopStorageStrategy }
]
```

```ts
// noop-storage.ts
export class NoopStorageStrategy implements StorageStrategy {
  async save() { /* do nothing */ }
  async load() { return null; }
}
```

Then override in specific subtrees where you need real behavior.

### Q: Strategy vs Directive — when to use which?

| Use Case | Pattern |
|----------|---------|
| **A or B** (mutually exclusive) | Strategy via DI |
| **A and/or B** (composable) | Directives |

Strategy = *which* implementation runs (one at a time).
Directive = *whether* a behavior exists (stackable).

### Q: Can I switch strategies at runtime?

Not easily with static providers. Options:
1. Use a "router" strategy that delegates internally based on runtime conditions
2. Use a factory provider with runtime logic
3. Consider if this is actually a directive use case (opt-in at runtime)

---

## Directives

### Q: How do directives communicate with the host component?

Inject the host component:

```ts
@Directive({ selector: 'app-list[sortable]' })
export class Sortable {
  #list = inject(ListComponent);

  constructor() {
    effect(() => {
      const sorted = [...this.#list.items()].sort(/*...*/);
      this.#list.displayItems.set(sorted);
    });
  }
}
```

The component exposes a writable signal (`displayItems`), the directive writes to it.

### Q: What about directive-to-directive communication?

Option 1: One directive injects the other:

```ts
@Directive({ selector: 'app-list[filterable]' })
export class Filterable {
  sortable = inject(Sortable, { optional: true });
}
```

Option 2: Use a coordinator directive (shown in slides with `AutoSaveable`).

Option 3: Communicate through the shared host component's signals.

### Q: Isn't injecting the component tight coupling?

Yes, and it's **intentional**. The directive is designed specifically for this component. The selector `app-list[sortable]` already declares this coupling — the inject just makes it work.

If you need the directive to work with multiple components, define an interface/token.

---

## hostDirectives

### Q: Can I conditionally include a hostDirective?

No. `hostDirectives` is static. For conditional composition, use regular directives on the template:

```html
<!-- Conditional: use template -->
<app-list [sortable]="needsSorting ? '' : null" />

<!-- Fixed bundle: use hostDirectives -->
<app-list powerList />
```

### Q: What's the performance impact of hostDirectives?

Minimal. Angular instantiates the directives only when the host is created. Same as having them in the template.

### Q: Can hostDirectives be nested (directive with hostDirectives that has hostDirectives)?

Yes, it composes. But be careful — deep nesting can hide behavior. Usually 1 level is enough.

---

## General Architecture

### Q: How do you decide when to extract vs. keep in component?

Use the **Feature × Context matrix** (the Map step):
1. If it's in ALL contexts → keep in component (baseline)
2. If it varies by context → extract

Also look for **tells**:
- Structural flag (`showX`) → Content Projection
- Behavioral bundle (flag + services) → Strategy
- Composable opt-in → Directive

### Q: What about performance? Doesn't this add overhead?

Negligible. Angular's DI and directive instantiation are highly optimized. The cognitive overhead of a mega component costs far more in developer time than a few extra class instantiations.

### Q: How do you handle testing with this architecture?

Each piece is testable in isolation:
- **Content Projection:** Test the shell component, test projected content separately
- **Strategy:** Mock the token, test component behavior
- **Directive:** Test directive with a minimal host, or test on the real component

```ts
// Testing a strategy
TestBed.configureTestingModule({
  providers: [
    { provide: STORAGE_STRATEGY, useClass: MockStorageStrategy }
  ]
});
```

### Q: Doesn't this create too many files?

It creates **focused** files. Trade-off:
- Before: 1 file with 2000 lines, hard to navigate
- After: 10 files with 200 lines each, easy to find what you need

The total complexity is the same — it's just organized.

---

## Real-World Scenarios

### Q: What about forms with different validation per context?

Strategy pattern for validators:

```ts
export const VALIDATION_STRATEGY = new InjectionToken<ValidationStrategy>('Validation');

// Strict validation for admin
{ provide: VALIDATION_STRATEGY, useClass: StrictValidation }

// Lenient for public forms
{ provide: VALIDATION_STRATEGY, useClass: LenientValidation }
```

### Q: How do you handle feature flags with this approach?

Feature flags are a **WHERE** decision. Options:
1. Different routes load different component compositions
2. Provider factory checks feature flag:

```ts
{
  provide: STORAGE_STRATEGY,
  useFactory: (features: FeatureService) =>
    features.isEnabled('cloudSync')
      ? new ServerStorageStrategy()
      : new LocalStorageStrategy(),
  deps: [FeatureService]
}
```

### Q: What if the team isn't familiar with these patterns?

Start small:
1. Pick ONE mega component
2. Extract ONE thing (usually the easiest structural flag)
3. Show the before/after
4. Let the pattern spread organically

Don't refactor everything at once.

---

## Quick Reference

| Tell | Pattern | Separates |
|------|---------|-----------|
| Cross-context drift | Map (Feature × Context) | **WHERE** |
| Structural flags (`showX`) | Content Projection | **WHAT** |
| Behavioral bundles | Strategy via DI | **HOW** |
| Composable opt-ins | Directives | **WHETHER** |
| Same combo 3x | hostDirectives | **NAME** |
