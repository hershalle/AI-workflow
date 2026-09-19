# Changelog Guide

Write for stakeholders deciding what to test, announce, or measure.

## Scope

- A changelog covers exactly one release tag. Derive it from the commit range between the previous tag and this one (`git log <previous-tag>..<tag>`).
- One entry per ticket, not per commit. Group every commit that served the same ticket into a single entry, including follow-up fixes made later in the release.
- Take ticket IDs from merge commits, PR titles and branch names. Work with no ticket gets an entry with no parentheses, never an invented ID.
- Read the diffs before writing an entry. Commit messages describe intent ("Refactor", "PR notes"), not user-visible outcome.

## Structure

- First line: the product name and release version, following the project format.
- Each entry: bold title, ticket ID in parentheses, then the prose in the same paragraph.
- Order by user-visible impact, biggest first. Analytics, logging, infrastructure and tooling go last.
- One paragraph per entry. When a ticket also carried an unrelated small fix, append it to the same paragraph after "Also:" instead of giving it its own entry.

## What an entry says

- Fixes: what the user experienced before, then that it is fixed. The symptom is the part the reader recognizes.
- Features: what it does, where in the app it appears, and the condition that makes it appear.
- Anything measured in money or data: name the consequence (ad revenue, under-counted subscriptions, opt-in rates we could not see).
- State it in the entry when a feature is in the build but switched off.
- State any caveat the team will hit: applies on next launch, behind a feature flag, one-time migration on first open.

## Leave out

- Localization and translation work. Never gets an entry.
- Type names, function names, file paths, commit hashes. Describe behavior in the app's own words.
- Ticket-internal churn: rebases, compile fixes, self-review passes, PR notes.

## Voice

- Plain language a non-engineer reads without stopping. "The same person could end up saved several times", not "phone numbers were not normalized to E.164".
- Past tense for the old broken behavior, present tense for what the app does now.

## Notes to the author

- After the changelog, flag what needed judgment: a ticket you grouped or split yourself, a feature you found disabled, work you could not attribute to a ticket. Keep these outside the changelog body so it stays sendable as-is.

## From commits to a release note

Suppose a fictional release contains three commits for `IOS-123`: "Fix duplicate contacts," "Handle formatted phone numbers," and "PR feedback." After reading the diffs, you establish that they all fix the same import behavior.

The changelog gets one entry describing the result:

> CallApp 1.30
>
> **Contacts no longer appear twice after import** (IOS-123) Importing the same person with differently formatted phone numbers could create duplicate contacts. These numbers now match the existing contact during import.

The ticket and version here are examples; use the actual release's identifiers when writing a changelog.
