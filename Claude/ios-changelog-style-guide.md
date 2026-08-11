# iOS Changelog Style Guide

## Scope

- A changelog covers exactly one release tag. Derive it from the commit range between the previous tag and this one (`git log <previous-tag>..<tag>`).
- One entry per ticket, not per commit. Group every commit that served the same ticket into a single entry, including follow-up fixes made later in the release.
- Take ticket IDs from merge commits, PR titles and branch names. Work with no ticket gets an entry with no parentheses, never an invented ID.
- Read the diffs before writing an entry. Commit messages describe intent ("Refactor", "PR notes"), not user-visible outcome.

**Why:** The reader is a stakeholder deciding what to test, announce or measure, not someone reviewing the branch. Twenty commits titled "Refactor" are one line to them.
**How to apply:** For each ticket ask: "what would a user or a dashboard notice?" That answer is the entry. If nothing would, the ticket may not need an entry.

## Structure

- First line: `CallApp iOS <version>`.
- Each entry: bold title, ticket ID in parentheses, then the prose in the same paragraph.
- Order by user-visible impact, biggest first. Analytics, logging, infrastructure and tooling go last.
- One paragraph per entry. When a ticket also carried an unrelated small fix, append it to the same paragraph after "Also:" instead of giving it its own entry.

## What an entry says

- Fixes: what the user experienced before, then that it is fixed. The symptom is the part the reader recognizes.
- Features: what it does, where in the app it appears, and the condition that makes it appear.
- Anything measured in money or data: name the consequence (ad revenue, under-counted subscriptions, opt-in rates we could not see).
- State it in the entry when a feature is in the build but switched off.
- State any caveat the team will hit: applies on next launch, behind a Remote Config flag, one-time migration on first open.

**Why:** A changelog is read to decide what to test and what to tell users. An entry that hides "this is disabled" or "this needs a flag turned on" costs a QA cycle or produces a false announcement.
**How to apply:** Before finishing an entry, ask what would make a reader feel misled after they open the build. Put that in the entry.

## Leave out

- Localization and translation work. Never gets an entry.
- Type names, function names, file paths, commit hashes. Describe behavior in the app's own words.
- Ticket-internal churn: rebases, compile fixes, self-review passes, PR notes.

## Voice

- Plain language a non-engineer reads without stopping. "The same person could end up saved several times", not "phone numbers were not normalized to E.164".
- Past tense for the old broken behavior, present tense for what the app does now.

## Notes to the author

- After the changelog, flag what needed judgment: a ticket you grouped or split yourself, a feature you found disabled, work you could not attribute to a ticket. Keep these outside the changelog body so it stays sendable as-is.
