# Test Guide

## Testing

- Keep the test suite small and focused. Prioritize mistakes that are hard to notice during manual QA or easy to miss when checking only the happy path.
- Use tests for complex transformations, calculations, and edge cases. For example, time abbreviations should be checked around the boundaries where the output changes from seconds to minutes or hours.
- Test behavior implemented by our code, rather than language or library behavior itself.

## Test readability

- A new developer should grasp *what* is being tested and roughly *why* from the test file alone, without opening production code.
- Make the thing under test obvious through structure, not comments:
  - The suite or test group names the production symbol or behavior under test.
  - Test container or file name matches the focus (`LatestSearchOrCallDateTests`, not `RemoteContactEntityTests`).
  - Test names describe the scenario in plain English; the subject is implicit ("Updates after searching", not "testSearch").
- Use domain language in setup. `contact` over `entity`. `yesterdaySearch` / `todayCall` over `t0` / `t1`. Names should let the reader picture the scene.

## Comments in tests

If a test's purpose is unclear, improve its name and structure before adding a comment. Use comments for context those cannot convey.
