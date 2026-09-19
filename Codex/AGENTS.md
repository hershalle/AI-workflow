# Global Instructions

The shared workflow files live in `ai-workflow` inside the Codex home directory
(`$CODEX_HOME` when set, otherwise `~/.codex`). Resolve all guide paths below from
that directory, not from the current project or working directory.

Before working, read and follow `ai-workflow/development-guide.md`.

Read and follow additional guides when relevant:

- When writing or reviewing tests: `ai-workflow/test-guide.md`.
- When reviewing code: `ai-workflow/code-review-guide.md` and `ai-workflow/test-guide.md`.
- When writing changelogs: `ai-workflow/changelog-guide.md`.
- For iOS/Swift development or code review: also read `ai-workflow/ios-style-guide.md` and `ai-workflow/ios-test-style-guide.md`.

Read the files explicitly, including shared guides referenced by an iOS guide;
do not rely on Claude's `@` import syntax. Resolve those references from the same
`ai-workflow` directory. These are global defaults; follow more specific project
instructions when they override them. If a guide is unavailable, report the missing path.
