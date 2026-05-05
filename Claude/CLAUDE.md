# Global Instructions

@ios-style-guide.md

## Code Review
- When reviewing a branch, present each issue with both the problematic code and the suggested solution as code blocks, so the diff is immediately visible.
- For each issue, include the file name, the enclosing function or property name, and the line number(s).
- Format the issue header as a numbered list using a clickable markdown link to the file, followed by the enclosing symbol and line number, then the issue description. Example:
  ```
  Issues:
   1. [ABConversionSearchScreenVisibility.foo](path/to/ABConversionSearchScreenVisibility.swift)():12: <The issue description>
  ```
