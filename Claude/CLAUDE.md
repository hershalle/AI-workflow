# Global Instructions

@ios-style-guide.md

## Code Review
- When reviewing a branch, present each issue with both the problematic code and the suggested solution as code blocks, so the diff is immediately visible.
- For each issue, include the file name, the enclosing function or property name, and the line number(s).
- Format the issue header as a numbered list with the enclosing type and symbol, followed by the line number, then the issue description. Do not include the file path or a markdown link. Example:
  ```
  Issues:
   1. CANotificationsManager.shouldSchedule(_:):132: <The issue description>
  ```
