# Decoupling in Angular: Letting the Code Tell You What It Needs

## Short Abstract (~150 words)

We rewrote Explore from scratch. Clean slate, no legacy baggage. Six months later: 23 inputs, 14 outputs, and a template drowning in `@if` blocks. We didn't inherit this mess - we built it, one reasonable decision at a time.

This talk walks through a practical progression of decoupling techniques: content projection for structural variation, strategy via DI for mutually exclusive behaviors, and directive composition for behaviors that accumulate. We'll see where each pattern fits, where it breaks, and the signals that tell you it's time to reach for the next tool.

The core insight: two times is a coincidence, three times is a concept. When the same directive combination repeats, the code is telling you something is missing. I'll show how to promote those patterns using `hostDirectives`, making implicit coupling explicit.

You'll leave with a decision framework - not a prescription, but a mental model for letting the code guide your abstractions.

---

## Long Abstract (~250 words)

We rewrote Explore from scratch. Clean slate. No legacy baggage. Month one: a component with four inputs, clear responsibility, easy to test. Six months of feature expansion later: 23 inputs, 14 outputs, and a template that opened with three nested `@if` blocks. We didn't inherit this mess - we built it, one reasonable decision at a time.

This talk is about how we took it apart, and the mental model I now use to recognize when it's time to restructure before the debt compounds.

I'll walk through a practical progression of decoupling techniques: content projection for structural variation, strategy via DI for mutually exclusive behaviors, and directive composition when behaviors need to accumulate rather than compete. Along the way, we'll confront the uncomfortable truth: composition gets noisy. Five directives on one element might be decoupled, but the usage isn't clean.

That's where the core insight comes in: two times is a coincidence, three times is a concept. Repeated compositions are a signal - the code is telling you there's a concept missing from your model. I'll show how to promote those patterns into named directives using `hostDirectives`, and how the promoted directive becomes the natural home for coordination logic.

You'll leave with:

- A decision framework for choosing abstractions as requirements evolve
- Concrete signals that tell you when to reach for the next tool
- Practical patterns using Angular 19+ features

Good abstractions aren't chosen. They're discovered.

