# Global Instructions

The shared workflow files live in `ai-workflow` inside the Codex home directory
(`$CODEX_HOME` when set, otherwise `~/.codex`). Resolve these paths from that
directory, not from the current project or working directory.

Before working, read and follow:

- `ai-workflow/CLAUDE.md` for the shared code-review rules.
- `ai-workflow/ios-style-guide.md` for writing style, development conventions, and Git rules.
- `ai-workflow/ios-test-style-guide.md` for test selection and readability.
- `ai-workflow/ios-changelog-style-guide.md` for release changelogs.

Read these files explicitly; do not rely on the `@` imports in `CLAUDE.md` being
expanded. Apply iOS-specific rules to iOS work and changelog rules to changelog
tasks. These are global defaults; follow more specific project instructions when
they override them. If a shared file is unavailable, report the missing path.

When asked to review a product story for development readiness, also read and
follow `ai-workflow/ios-story-review-prompt.md`. Its project context and review-only
constraints apply only to that task, not to general development work.
