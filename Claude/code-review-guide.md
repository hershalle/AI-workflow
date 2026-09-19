# Code Review Guide

- When reviewing a branch, present each issue with both the problematic code and the suggested solution as code blocks, so the diff is immediately visible.
- For each issue, include the file name, the enclosing function or property name, and the line number(s).
- Format the issue header as a numbered list with the enclosing type and symbol, followed by the line number, then the issue description. Do not include the file path or a markdown link.
- Split the review into a required `Issues` section for defects in the code, plus optional follow-up sections. Never list a follow-up finding as a code issue:
  - `Missing Tests` for behavior that is not covered.
  - `Missing Localization` for user-facing text that bypasses the project localization mechanism. Use the platform guide to distinguish localized literals from unlocalized strings.
- Follow-up sections are suggestions, not blockers. Mark each one `(optional)` in its header, and omit a section entirely when it would be empty.
- Entries in every section use the same header format, numbered independently per section, pointing at the production symbol.
- Don’t ask for a test just because a function was added. Explain what could go wrong that the test would catch.

## A finished review entry

The following is an illustrative review of a scheduler whose documented rule requires both notification permission and an enabled feature flag. The filename and line number are examples.

### Issues

1. NotificationScheduler.shouldSchedule:132: Notifications are scheduled when the feature is disabled

   File: `NotificationScheduler.swift`, property `shouldSchedule`, line 132.

   Using `||` allows scheduling whenever either condition is true. A user with notification permission still gets notifications after the feature is disabled. Require both conditions.

   Current:
   ```swift
   var shouldSchedule: Bool {
       isAuthorized || isEnabled
   }
   ```

   Suggested:
   ```swift
   var shouldSchedule: Bool {
       isAuthorized && isEnabled
   }
   ```

If the implementation were already correct but lacked a test for disabling the feature, that would belong under `Missing Tests (optional)` instead.
