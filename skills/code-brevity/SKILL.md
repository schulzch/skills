---
name: code-brevity
description: >-
  Ruthlessly apply YAGNI and KISS to a specified source module or file:
  automatically remove low-risk accidental complexity, dead indirection,
  repetition, and speculative machinery while preserving behavior and
  readability, then expose larger opportunities to dismantle Architecture
  Theater. Use when asked to clean up, simplify, shorten, modernize, make
  idiomatic, reduce duplication, challenge abstractions, or improve an existing
  code file or module.
---

# Code Brevity

Make the requested module or file as simple as its current requirements permit. Treat every abstraction, layer, option, and extension point as guilty until justified by present use. Optimize for fewer concepts and clearer control flow, which should naturally result in significantly lower line counts and reduced cognitive load.

## Workflow

1. Read the complete target and enough neighboring code, tests, configuration, and repository guidance to understand its role and local conventions.
2. Check the working tree before editing. Preserve unrelated user changes and avoid broad formatting churn.
3. Establish what the code demonstrably needs today from call sites, tests, configuration, and active implementations. Do not preserve machinery solely for imagined future requirements.
4. Automatically harvest low-hanging fruit:
   - delete dead code, unused parameters, redundant state, speculative options, and needless configurability when evidence makes removal safe;
   - inline one-use helpers, pass-through wrappers, and aliases that add vocabulary without insight;
   - collapse redundant branches, conversions, temporaries, forwarding methods, and duplicate representations;
   - replace home-grown machinery with direct language or standard-library features;
   - flatten needless nesting and fragmented control flow;
   - remove comments made unnecessary by clearer code.
5. Apply YAGNI ruthlessly: retain an extension point only when an active caller, test, requirement, or multiple real implementations justify it. Apply KISS ruthlessly: prefer the smallest direct design that explains the current behavior.
6. Preserve observable behavior, required public APIs, error semantics, ordering, side effects, and material performance characteristics unless the user explicitly authorizes changes. Treat suspicious behavior as a possible bug; do not silently fix it as cleanup.
7. Prefer repository conventions, but do not use local precedent to excuse obvious accidental complexity. Keep explicit code only when compression genuinely introduces structural obscurity.
8. Run the narrowest relevant formatter, static checks, and tests available. Expand validation when the change is broad or risky. If validation cannot run, state that plainly.
9. Review the diff for accidental semantic changes, needless churn, weakened diagnostics, and readability regressions.
10. After implementing and validating the safe simplifications, inspect the target and its immediate boundaries for Architecture Theater and report the remaining teardown opportunities to the user.

## Judgment Rules

- Prefer deleting concepts over rearranging them.
- Demand concrete evidence for factories, strategies, interfaces, registries, adapters, generic frameworks, event layers, configuration surfaces, and dependency injection.
- Remove duplication when the shared concept is stable and the resulting abstraction has a clear name; tolerate small duplication when sharing would create coupling or ceremony.
- Prefer guard clauses or direct expressions when they reduce nesting without hiding control flow.
- Do not combine distinct responsibilities merely to reduce file size.
- Do not introduce a new abstraction merely to perform the refactor.
- Do not introduce dependencies, break required interfaces, or refactor neighboring modules unless required for a coherent in-scope change.
- Keep comments that explain constraints, intent, invariants, or non-obvious tradeoffs. Remove comments that only narrate obvious syntax.
- Stop and report opportunities instead of editing when the user asks for review only.

## Architecture Theater Report

Call out structures whose ceremony exceeds their demonstrated value, especially:

- interfaces or traits with one implementation and no credible substitution need;
- factories, builders, registries, or dependency-injection plumbing around straightforward construction;
- adapters and service layers that only forward calls or rename data;
- generic frameworks supporting one concrete case;
- event or message indirection inside a synchronous local flow;
- configuration for choices the product does not actually offer;
- data models duplicated across layers without a real boundary;
- speculative extensibility justified only by "might need later."

Do not automatically perform a teardown when it crosses module boundaries, breaks an API, changes ownership, or requires a product decision. Present each opportunity with the structure to remove, the simpler replacement, concrete evidence, expected payoff, and principal risk. Rank opportunities by payoff versus disruption. Say explicitly when no credible Architecture Theater remains.

## Final Response

Lead with the simplifications already made. Briefly explain the meaningful changes and validation. Then add a concise `Architecture Theater` section containing the ranked teardown opportunities that require user authorization. Avoid a line-by-line recap.
