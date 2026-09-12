# AI-workflow

Personal iOS development, testing, code-review, changelog, and story-review instructions for Claude Code and Codex.

## Shared files

`Claude/` remains the source of truth so existing Claude symlinks keep working.
Edit the guides there to update both assistants. `Codex/AGENTS.md` is a small
entry point that explicitly instructs Codex to read those same files.

The story-readiness prompt is used on request, rather than as an instruction for
every coding task.

## Claude setup

Run from this repository's root. Create links only for files that are not already
installed; inspect existing files or links before replacing them.

```bash
mkdir -p "$HOME/.claude"
for name in CLAUDE.md ios-style-guide.md ios-test-style-guide.md ios-changelog-style-guide.md ios-story-review-prompt.md; do
  ln -s "$PWD/Claude/$name" "$HOME/.claude/$name"
done
```

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
