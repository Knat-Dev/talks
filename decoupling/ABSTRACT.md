# Decoupling in Angular: Letting the Code Tell You What It Needs

## Short Abstract (~150 words)

We built a data grid from scratch. Clean slate, no legacy baggage. Six months later: a component that worked perfectly - for exactly one use case. Then product asked for the same grid in two more contexts. The code wasn't wrong. It was welded shut.

This talk walks through a practical progression of decoupling techniques: inputs/outputs as the baseline, content projection for structural variation, strategy via DI for mutually exclusive behaviors, and directive composition for behaviors that accumulate. We'll see where each pattern fits, where it breaks, and the signals that tell you it's time to reach for the next tool.

The core insight: two times is a coincidence, three times is a concept. When the same pattern repeats, the code is telling you there's an abstraction missing.

You'll leave with a decision framework for recognizing these signals - so you can restructure before the reuse request hits.

---

## Long Abstract (~250 words)

We built a data grid from scratch. Clean slate. No legacy baggage. Month one: two inputs, clear responsibility, easy to test. Six months later: a component that worked perfectly - for exactly one use case.

Then product asked for the same grid in two more contexts. Same core, different behaviors. And we discovered the component was welded shut - every variation required threading another flag through the entire stack.

This talk is about how we took it apart, and the mental model I now use to recognize when it's time to restructure before the reuse request arrives.

I'll walk through a practical progression of decoupling techniques: inputs/outputs as the baseline (and where they break down), content projection for structural variation, strategy via DI for mutually exclusive behaviors, and directive composition when behaviors need to accumulate rather than compete. Along the way, we'll confront the uncomfortable truth: composition gets noisy - but repetition reveals missing concepts.

That's where the core insight comes in: two times is a coincidence, three times is a concept. Repeated patterns are a signal. Name them.

You'll leave with:

- A decision framework for recognizing signals in your own code
- The "promotion rule" for turning implicit patterns into explicit abstractions
- Practical patterns using Angular's hostDirectives

Good abstractions aren't chosen. They're discovered.
