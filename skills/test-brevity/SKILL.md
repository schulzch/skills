---
name: test-brevity
description: >-
  Ruthlessly reduce an existing test suite to the smallest set that protects
  concrete application behavior. Delete tests of library internals, tautologies,
  redundant cases, and test ceremony; narrow integration tests to the project's
  own wiring and configuration. Use when asked to prune, shorten, simplify,
  deduplicate, or challenge existing tests or reduce test maintenance.
---

# Test Brevity

Every test must earn its place by catching a concrete regression in this application. Treat tests, assertions, fixtures, mocks, and helpers as guilty until their value is demonstrated. Minimize maintenance and concepts while preserving distinct product guarantees. Test count and coverage percentage are not goals.

## Workflow

1. Check the working tree and repository guidance. Read the production code, its callers, and the existing test commands. Establish the relevant baseline before editing; distinguish existing failures and unavailable infrastructure from regressions.
2. Inventory every test in the requested scope before choosing deletions. For a module cleanup, inspect the package's neighboring tests and adapters too: tests of the same behavior often live elsewhere. Honor an explicitly narrower scope and keep unrelated packages outside the cleanup.
3. Classify each test and its assertions in working notes:
   - **Application:** identify the concrete requirement or regression it protects.
   - **Library:** it mostly reconfirms behavior supplied by a dependency or the language.
   - **Mixed:** it exercises a dependency through our configuration, adapter, or public contract.
   Record a keep, delete, or shorten decision for every test. Merely reading a file is not an evaluation.
4. Delete Library tests and other Test Theater automatically. Shorten Mixed tests to assertions about our choices and integration. For overlapping Application tests, keep the clearest representative and only the distinct cases the others add.
5. Strip setup, fixtures, wrappers, mocks, and assertions made unnecessary by the deletions. Prefer direct setup and a small table of equivalent cases over a test framework inside the test suite. Keep independent behaviors independently diagnosable.
6. Run the repository's relevant formatter, checks, and remaining tests. Review the diff for lost product guarantees, weakened assertions, and tests whose expected result comes from the same implementation they are meant to verify.
7. Finish only after every in-scope test has a disposition and every retained test has a concrete purpose. Passing tests and a smaller diff alone do not establish completion. Report validation limits and unresolved decisions plainly.

## Judgment Rules

- Ask: **What plausible change to our production code should make this test fail?** A test that only fails when a dependency breaks is a deletion candidate.
- Remove tests that reprove standard routing, JSON parsing, collection operations, generated getters, dependency serialization, or mock behavior without checking an application choice. Preserve tests of our route mapping, persisted fields, custom validation, security configuration, and adapter boundaries.
- For Mixed tests, retain our configured origins, methods, limits, headers, and error mapping. Delete exhaustive permutations of dependency defaults, exact dependency error wording, and library protocol details that our adapter does not implement.
- Keep serialization tests when they protect required application state across persistence or recovery. Keep concurrency tests when they protect our atomicity, transaction boundaries, or admission rules. Using a library is not grounds for deleting a test.
- Collapse cases that exercise the same branch and guarantee. Retain meaningful boundaries, distinct failure paths, ownership checks, ordering, expiry, retry behavior, and state preservation when they can regress independently.
- Delete tautologies, assertion-free tests, tests that only assert a stub's configured return, and expected values computed by the function under test. For valuable behavior hidden behind such a test, replace the assertion with an independent observable expectation.
- Prefer a few behavior-focused scenarios to one test per method or line. Avoid merging unrelated scenarios into a long omnibus test just to lower the count.
- Simplify mocks and fixtures to the data and interactions the scenario needs. Assert interaction counts or call order only when they are part of the required behavior. Remove one-use forwarding helpers and speculative test infrastructure.
- Preserve production behavior during test cleanup. Existing failures are evidence to investigate, not permission to delete coverage, add skips, loosen assertions, or change expected results until the suite passes.
- Preserve useful failure diagnostics. Choose the smallest suite that still explains which guarantee broke; brevity does not justify cryptic assertions or new abstractions.
- For a review-only request, return the dispositions and proposed reductions without editing.

## Final Response

Lead with the reductions made, the important guarantees retained, and validation results. State actual removed or consolidated counts when useful. Report remaining Test Theater only where a concrete requirement or scope decision prevents removal, with evidence and the proposed minimum replacement. Avoid dumping the full inventory unless requested.
