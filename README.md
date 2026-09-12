# AI-workflow

Personal general and iOS development, testing, code-review, changelog, and story-review instructions for Claude Code and Codex.

## Shared files

`Claude/` remains the source of truth so existing Claude symlinks keep working.
Edit the guides there to update both assistants. `Codex/AGENTS.md` is a small
entry point that explicitly instructs Codex to read those same files.

The story-readiness prompt is used on request, rather than as an instruction for
every coding task.

## Guide loading

Both assistants read `development-guide.md` for all work. They read `test-guide.md`,
`code-review-guide.md`, and `changelog-guide.md` for the corresponding tasks.
iOS work additionally loads the iOS development and test guides. All projects use
the general changelog guide. The story prompt stays on demand.
Swift syntax, SwiftUI, and String Catalog exceptions do
not apply to general development.

New guide files must be referenced in both entry points. Both assistants see them
through directory symlinks, so no additional symlinks are needed.

## Claude setup

Run from this repository's root. Create links only for files that are not already
installed; inspect existing files or links before replacing them.

```bash
mkdir -p "$HOME/.claude"
ln -s "$PWD/Claude" "$HOME/.claude/ai-workflow"
ln -s "$PWD/Claude/CLAUDE.md" "$HOME/.claude/CLAUDE.md"
```

Start a new Claude session after setup. When migrating from individual guide
symlinks, remove those old links only after verifying the directory link works.
Keep the `CLAUDE.md` entry-point link.

## Codex setup

Run from this repository's root:

```bash
codex_dir="${CODEX_HOME:-$HOME/.codex}"
mkdir -p "$codex_dir"
ln -s "$PWD/Claude" "$codex_dir/ai-workflow"
ln -s "$PWD/Codex/AGENTS.md" "$codex_dir/AGENTS.md"
```

These commands do not overwrite existing files. If `AGENTS.md` already contains
instructions, merge this entry point into it or preserve those instructions before
replacing it with a symlink. An existing `AGENTS.override.md` takes precedence over
`AGENTS.md`; check it if these instructions do not load.

Start a new Codex session after setup. Ask it to list the instruction files it read
and summarize your iOS conventions. This setup requires local access to this
checkout; another machine or a cloud environment needs its own installation.

See the [official AGENTS.md documentation](https://learn.chatgpt.com/docs/agent-configuration/agents-md)
for global discovery and project overrides.
