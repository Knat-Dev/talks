# Decoupling in Angular: Letting the Code Tell You What It Needs

## Short Abstract (~150 words)

We built a data grid from scratch. Clean slate. Month one: two inputs, clear responsibility, easy to test. Six months later: a component that worked perfectly, for exactly one use case. Then product asked for the same grid in two more contexts. Same core, different behaviors. And we discovered the component was welded shut, every variation required threading another flag through the entire stack.

This talk walks through a practical progression of decoupling techniques: inputs and outputs as the baseline, strategy via dependency injection for mutually exclusive behaviors, and directive composition when behaviors accumulate. For each tool, we'll see where it fits, where it breaks down, and the signal that tells you it's time to reach for the next one.

That leads to the core insight of the talk: two times is a coincidence, three times is a concept. Repeated patterns are signals. Name them.

You'll leave with a decision framework for matching code signals to decoupling tools, the "promotion rule" for when the same directive combination appears three times, and a practical pattern for using Angular's hostDirectives to make implicit concepts explicit.

---

## Long Abstract (~250 words)

We built a data grid from scratch. Clean slate. Month one: two inputs, clear responsibility, easy to test. Six months later: a component that worked perfectly, for exactly one use case. Then product asked for the same grid in two more contexts. Same core, different behaviors. And we discovered the component was welded shut, every variation required threading another flag through the entire stack.

This talk is about how we took it apart, and the mental model I now use to recognize when it's time to restructure before the reuse request arrives.

I'll walk through a practical progression of decoupling techniques: inputs and outputs as the baseline, strategy via dependency injection for mutually exclusive behaviors, and directive composition when behaviors accumulate. For each tool, we'll see where it fits, where it breaks down, and the signal that tells you it's time to reach for the next one.

That leads to the core insight of the talk: two times is a coincidence, three times is a concept. Repeated patterns are signals. Name them.

You'll leave with:

- A decision framework for matching code signals to decoupling tools
- The "promotion rule": when the same directive combination appears three times, it's earned a name
- A practical pattern for using Angular's hostDirectives to make implicit concepts explicit

Good abstractions aren't chosen. They're discovered.
