# iOS Test Style Guide

## Testing

- Every test must earn its place. If deleting it would let no real regression slip through, delete it.
- Each test should target a distinct bug class. Additive vs subtractive mutations, presence vs correctness, init vs mutation — different classes deserve separate tests. Same class — keep one.
- Don't test language or library contracts (e.g. `Array.max()` on empty, `Equatable` synthesis you didn't write).

**Why:** Tests are code we maintain. A test that doesn't protect against a real regression is debt without benefit; a duplicated test gives false coverage confidence.
**How to apply:** Before writing a test, name the regression it would catch. Before keeping it, ask: "if a teammate broke this contract, would this be the test that fails?" If a sibling test already would, drop one.

## Test readability

- A new developer should grasp *what* is being tested and roughly *why* from the test file alone, without opening production code.
- Make the thing under test obvious through structure, not comments:
  - `@Suite("Type.symbol")` names the focus.
  - Struct name matches the focus (`LatestSearchOrCallDateTests`, not `RemoteContactEntityTests`).
  - Test names describe the scenario in plain English; the subject is implicit ("Updates after searching", not "testSearch").
- Use domain language in setup. `contact` over `entity`. `yesterdaySearch` / `todayCall` over `t0` / `t1`. Names should let the reader picture the scene.

**Why:** Tests are the most honest documentation of a contract. If a reader needs to open another file to understand what a test is verifying, the test has failed at that job.
**How to apply:** Read your new test as if you've never seen the codebase. If "what is this verifying?" isn't answerable from the test file alone, restructure — rename the suite, the struct, the test, the variables — until it stands alone.

## Comments in tests

- Same rule as production code: comments must earn their place.
- If a test's purpose isn't clear from its name and body, fix the name and body. Don't add a comment.

**Why:** Comments rot; names and structure stay honest because the compiler and the reader both touch them.
**How to apply:** Before writing a comment, try a rename or a restructure first. If the only thing the comment can convey is something the code genuinely can't (a non-obvious invariant that motivates the test's existence), then it earns its place.
