---
name: code-brevity
description: >-
  Ruthlessly apply YAGNI and KISS to a specified source module or file and its
  tests: prune tests down to those that protect concrete application behavior,
  automatically remove low-risk accidental complexity, dead indirection,
  repetition, and speculative machinery, tighten documentation without losing
  facts, then report Architecture Theater, remaining Test Theater, and noise
  (orphaned, regenerable, or redundant files). Use when asked to clean up,
  simplify, shorten, modernize, make idiomatic, reduce duplication, challenge
  abstractions, prune or deduplicate tests, reduce test maintenance, remove
  legacy leftovers, or tighten documentation in an existing code file or module.
---

# Code Brevity

Make the requested module or file, together with its tests and documentation, as simple as current requirements permit. Treat every abstraction, layer, option, extension point, test, assertion, fixture, mock, helper, and paragraph of prose as guilty until justified by present use. Optimize for fewer concepts, clearer control flow, and lower maintenance. Line count, test count, and coverage percentage are consequences, not goals.

## Scope

The target is the named module or file plus its tests, its documentation, and the files that directly reference it or are referenced by it. Tests are always in scope: inspect the package's neighboring tests and adapters too, because tests of the same behavior often live elsewhere. Honor an explicitly narrower request ("code only", "tests only") and keep unrelated packages outside the cleanup. For a review-only request, return dispositions and proposed reductions without editing.

## Workflow

1. Check the working tree and repository guidance. Preserve unrelated user changes and avoid broad formatting churn. Read the complete target, its callers, its tests, configuration, and the test commands. Establish a baseline by running the relevant tests; distinguish existing failures and unavailable infrastructure from regressions.
2. Establish what the code demonstrably needs today from call sites, tests, configuration, and active implementations. Do not preserve machinery solely for imagined future requirements.
3. **Tests first.** Inventory every test in scope before choosing deletions. Classify each test and its assertions in working notes and record a keep, delete, or shorten decision for every test; merely reading a file is not an evaluation.
   - **Application:** protects a concrete requirement or regression.
   - **Library:** mostly reconfirms behavior supplied by a dependency or the language.
   - **Mixed:** exercises a dependency through our configuration, adapter, or public contract.
4. Delete Library tests and other Test Theater automatically, including whole test files, fixtures, mocks, and helpers that are orphaned or made unnecessary. Shorten Mixed tests to assertions about our choices and integration. For overlapping Application tests, keep the clearest representative and only the distinct cases the others add. Strip setup made unnecessary by the deletions.
5. Re-run the tests. The reduced suite is the safety net for the code step; it must pass before the code changes start.
6. **Then the code.** Automatically harvest low-hanging fruit:
   - delete dead code, unused parameters, redundant state, speculative options, and needless configurability when evidence makes removal safe;
   - inline one-use helpers, pass-through wrappers, and aliases that add vocabulary without insight;
   - collapse redundant branches, conversions, temporaries, forwarding methods, and duplicate representations;
   - replace home-grown machinery with direct language or standard-library features;
   - flatten needless nesting and fragmented control flow;
   - remove comments made unnecessary by clearer code.
   Apply YAGNI: retain an extension point only when an active caller, test, requirement, or multiple real implementations justify it. Apply KISS: prefer the smallest direct design that explains the current behavior. Tests made dead by deleted code are removed in the same step.
7. If a code change touches behavior that no remaining test covers, add the smallest independent test for it or drop the change. A thinner safety net never justifies unguarded edits.
8. **Documentation.** Tighten losslessly (see Documentation Rules). Apply the net rule to everything written, comments included.
9. Run the narrowest relevant formatter, static checks, and the remaining tests. Expand validation when the change is broad or risky. If validation cannot run, state that plainly.
10. Review the diff for accidental semantic changes, lost product guarantees, weakened assertions, tests whose expected values come from the implementation they verify, weakened diagnostics, needless churn, and readability regressions.
11. Finish only after every in-scope test has a disposition and every retained test has a concrete purpose. Passing tests and a smaller diff alone do not establish completion.
12. Inspect the target and its immediate boundaries for Architecture Theater, remaining Test Theater, and noise, and produce the reports below.

## Judgment Rules: Code

- Prefer deleting concepts over rearranging them.
- Demand concrete evidence for factories, strategies, interfaces, registries, adapters, generic frameworks, event layers, configuration surfaces, and dependency injection.
- Remove duplication when the shared concept is stable and the resulting abstraction has a clear name; tolerate small duplication when sharing would create coupling or ceremony.
- Preserve observable behavior, public APIs, error semantics, ordering, side effects, and material performance characteristics unless the user explicitly authorizes changes. Internal signatures may be changed when every caller is inside the repository and the tests pass. Treat suspicious behavior as a possible bug; do not silently fix it as cleanup.
- Prefer repository conventions, but do not use local precedent to excuse obvious accidental complexity. Keep explicit code only when compression genuinely introduces structural obscurity.
- Prefer guard clauses or direct expressions when they reduce nesting without hiding control flow.
- Do not combine distinct responsibilities merely to reduce file size. Do not introduce a new abstraction merely to perform the refactor.
- Do not introduce dependencies, break public interfaces, or refactor neighboring modules unless required for a coherent in-scope change.
- Keep comments that explain constraints, intent, invariants, or non-obvious tradeoffs. Remove comments that only narrate obvious syntax.

## Judgment Rules: Tests

- Ask: **What plausible change to our production code should make this test fail?** A test that only fails when a dependency breaks is a deletion candidate.
- Remove tests that reprove standard routing, JSON parsing, collection operations, generated getters, dependency serialization, or mock behavior without checking an application choice. Preserve tests of our route mapping, persisted fields, custom validation, security configuration, and adapter boundaries.
- For Mixed tests, retain our configured origins, methods, limits, headers, and error mapping. Delete exhaustive permutations of dependency defaults, exact dependency error wording, and library protocol details our adapter does not implement.
- Keep serialization tests when they protect required application state across persistence or recovery. Keep concurrency tests when they protect our atomicity, transaction boundaries, or admission rules. Using a library is not grounds for deleting a test.
- Collapse cases that exercise the same branch and guarantee. Retain meaningful boundaries, distinct failure paths, ownership checks, ordering, expiry, retry behavior, and state preservation when they can regress independently.
- Delete tautologies, assertion-free tests, tests that only assert a stub's configured return, and expected values computed by the function under test. For valuable behavior hidden behind such a test, replace the assertion with an independent observable expectation.
- Prefer a few behavior-focused scenarios to one test per method or line, and a small table of equivalent cases to a test framework inside the suite. Do not merge unrelated scenarios into an omnibus test just to lower the count. Keep independent behaviors independently diagnosable.
- Simplify mocks and fixtures to the data and interactions the scenario needs. Assert interaction counts or call order only when they are part of the required behavior. Remove one-use forwarding helpers and speculative test infrastructure.
- Existing failures are evidence to investigate, not permission to delete coverage, add skips, loosen assertions, or change expected results until the suite passes.
- Preserve useful failure diagnostics. Choose the smallest suite that still explains which guarantee broke.

## Documentation Rules

- Tighten losslessly: shorten wording, merge duplicates, and state each fact once. Never drop a constraint, rationale, command, gotcha, or non-obvious tradeoff on your own.
- Removing a whole fact or section is a proposal for the Noise Report, not an edit.
- Net rule: after a run, documentation and comments are no longer than before. Changes replace existing text in place instead of appending, and a new paragraph appears only when an equal amount of text disappears elsewhere.
- Redundant prose that merely restates what the code expresses is removable noise; report it with evidence.

## Reports

Report only what the safe simplifications did not already resolve. Every entry names the evidence, the proposed minimum replacement or action, the expected payoff, and the principal risk, and entries are ranked by payoff versus disruption. Say explicitly when a report has nothing credible to list.

### Architecture Theater

Structures whose ceremony exceeds their demonstrated value, especially:

- interfaces or traits with one implementation and no credible substitution need;
- factories, builders, registries, or dependency-injection plumbing around straightforward construction;
- adapters and service layers that only forward calls or rename data;
- generic frameworks supporting one concrete case;
- event or message indirection inside a synchronous local flow;
- configuration for choices the product does not actually offer;
- data models duplicated across layers without a real boundary;
- speculative extensibility justified only by "might need later."

Do not tear these down automatically when the change crosses module boundaries, breaks a public API, changes ownership, or requires a product decision.

### Test Theater

Tests that remain only because a concrete requirement or a scope decision prevents removal. Give the evidence and the proposed minimum replacement. Do not dump the full inventory unless requested.

### Noise Report

Files and passages that are report-only candidates (non-test files are never deleted automatically). A candidate qualifies when at least one criterion holds:

- **Orphaned:** nothing references it (imports, links, build or CI configuration, tests, documentation).
- **Regenerable:** a generator, script, prompt, or documentation in the repository can recreate it, or it carries a generated header.
- **Little demonstrable use:** no active caller, requirement, or test justifies it (YAGNI), for example artifacts of a completed one-off task by an earlier agent or developer, such as reference assets that served a presentation.
- **Redundant:** it only restates what the code already expresses.

Evidence comes from reference search, git history (committed once and never touched, commit or PR naming a finished task, agent authorship), regenerability, and redundancy against the code. When use outside the repository is possible (presentations, other repositories), mark the entry unverified instead of guessing.

## Approval Follow-up

After the user approves selected Architecture Theater, Test Theater, or Noise Report entries, carry them out with the same validation and diff review as the main run. Do not widen the selection.

## Final Response

Lead with the simplifications already made: the test reductions (with actual removed or consolidated counts when useful), the important guarantees retained, the code and documentation changes, and validation results. Avoid a line-by-line recap. Then add three concise sections, `Architecture Theater`, `Test Theater`, and `Noise Report`, each a ranked list of entries that need the user's decision. State validation limits and unresolved decisions plainly.
