# Global Instructions

@ios-style-guide.md
@ios-test-style-guide.md
@ios-changelog-style-guide.md

## Code Review
- When reviewing a branch, present each issue with both the problematic code and the suggested solution as code blocks, so the diff is immediately visible.
- For each issue, include the file name, the enclosing function or property name, and the line number(s).
- Format the issue header as a numbered list with the enclosing type and symbol, followed by the line number, then the issue description. Do not include the file path or a markdown link.
- Split the review into a required `Issues` section for defects in the code, plus optional follow-up sections. Never list a follow-up finding as a code issue:
  - `Missing Tests` for behavior that is not covered.
  - `Missing Localization` for user-facing text that bypasses the String Catalog: a plain `String` shown to the user (`Text(title)` where `title` was built from literals), hand-rolled pluralization, or a literal ternary typed as `String`.
- Follow-up sections are suggestions, not blockers. Mark each one `(optional)` in its header, and omit a section entirely when it would be empty.
- A literal in `Text("…")`, `Button("…")`, `Label("…")`, or `.accessibilityLabel("…")` is localized by the String Catalog. Do not flag it.
- Entries in every section use the same header format, numbered independently per section, pointing at the production symbol.
- Only list a missing test that would catch a real regression, per the test style guide. Do not list one per new symbol.
- Example:
  ```
  Issues:
   1. CANotificationsManager.shouldSchedule(_:):132: <The issue description>

  Missing Tests (optional):
   1. CANotificationsManager.nextFireDate(_:):88: <The uncovered behavior and the regression it would catch>

  Missing Localization (optional):
   1. ReminderRow.body:24: <The text that bypasses the catalog and how to route it through a literal or String(localized:)>
  ```

**Why:** An untested or unlocalized path is a follow-up, not a defect in the shipped behavior. Mixing them into `Issues` makes the review read as more blocking than it is, and buries the real problems.
**How to apply:** For each finding, ask: "is the code wrong, or is it correct but untested / correct but unlocalized?" Anything other than wrong goes in a follow-up section.
