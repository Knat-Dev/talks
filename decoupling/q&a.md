# Q&A Cheat Sheet

## Strategy via DI

### "Why not use an abstract class instead of InjectionToken?"

You can! Abstract class as token — implementations extend it, no InjectionToken needed. Less boilerplate, no `@Inject()` decorator.

```ts
// Abstract class IS the token
export abstract class StorageStrategy {
  abstract save(key: string, data: unknown): Promise<void>;
  abstract load<T>(key: string): Promise<T | null>;
}

// Implementation extends it
export class LocalStorageStrategy extends StorageStrategy { ... }

// Provide it
providers: [{ provide: StorageStrategy, useClass: LocalStorageStrategy }]

// Inject — no @Inject needed
storage = inject(StorageStrategy);
```

**Trade-off**: Single inheritance limit — can only extend one class.

---

## Content Projection

### "What about @if for loading states? Should those be projected too?"

No. Only *structural* @ifs — where a boolean input controls what DOM exists.

Loading states, null guards, error states — those stay inside. They're not feature toggles, they're internal component logic.

---

## Directives

### "How does the directive get the Strategy? Who provides it?"

The Strategy is provided somewhere up the component tree (route, parent component, or root). The directive just injects it — Angular's hierarchical DI resolves it.

```ts
// Parent provides
@Component({
  providers: [{ provide: STORAGE_STRATEGY, useClass: ServerStorageStrategy }]
})
export class AdminPage { }

// Directive consumes
@Directive({ selector: 'app-list[persistable]' })
export class Persistable {
  #storage = inject(STORAGE_STRATEGY);  // Gets ServerStorageStrategy
}
```

---

## hostDirectives

### "Doesn't hostDirectives hide what's happening? Isn't that bad?"

Trade-off: You lose template visibility, but gain a named concept.

Three scattered attributes → one named thing you can document, test, discuss.

If visibility is critical, use regular directives. If the combo is a *concept* that repeats, name it with hostDirectives.

---

## General

### "When should I just use inputs instead of these patterns?"

Inputs are your default. Use extraction patterns when:

- You have 3+ boolean flags on a component
- Same file touched by every PR
- New devs can't understand the component without reading all the code

Don't extract preemptively. Wait for the pain.

### "How do I convince my team to adopt this?"

Start small. Pick ONE boolean flag in ONE component. Extract it. Show the before/after.

Don't rewrite everything. Let the patterns spread through example.
