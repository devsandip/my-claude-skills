# my-claude-skills

Claude Code skills and slash commands I use to keep my solo projects coherent across sessions, weeks, and breaks.

This is the public version of my personal workflow setup. It exists because I got tired of opening a Claude Code session two weeks after the last one and spending an hour figuring out what I'd already tried.

## What's here

### `project-journal/` — the skill

A skill for maintaining a `journal/` directory at the root of any coding project. Three artifact types:

- `INDEX.md` — live, mutable, always reflects today
- `entries/YYYY-MM-DD-HHMM-slug.md` — frozen, timestamped narrative entries, backward-linked
- `weekly/YYYY-Www-summary.md` — weekly synthesis of that week's daily entries

Think of it as a WBR (weekly business review, an Amazon thing) for solo builders. The skill handles three operations: bootstrap a journal on a new project, write a weekly summary, refresh the index when it's gone stale. Single daily entries during normal coding don't need the skill — they happen ambiently.

### `commands/` — the slash commands

Three slash commands that invoke the three skill operations from any Claude Code session: `/journal-bootstrap`, `/journal-weekly`, `/journal-refresh`.

### `reference/` — templates and snippets

The structural templates the skill produces (`INDEX.md`, `entry_template.md`, `weekly_template.md`), an opt-in/opt-out snippet for project-level `CLAUDE.md` or `AGENTS.md`, and a fallback paste-in prompt for sessions where slash commands aren't installed.

Reference material only. Not read by Claude Code at runtime.

## Install

```bash
# The skill (required)
cp -r project-journal ~/.claude/skills/

# The slash commands (optional but recommended)
mkdir -p ~/.claude/commands
cp commands/journal-*.md ~/.claude/commands/
```

Then in any Claude Code session:

- `/journal-bootstrap` — set up the journal on a new project
- `/journal-weekly` — write this week's summary
- `/journal-refresh` — refresh INDEX.md when it's drifted from reality

## What's not here

The personal `CLAUDE.md` convention files that this skill works best with. They're personal and project-specific. The short version of what they contain:

- A three-level `CLAUDE.md` hierarchy (global → all-coding-projects → per-project) with inheritance.
- A `WORKLOG.md` convention for end-of-session handoff notes.
- A documentation discipline (`/docs` at repo root, never duplicated across worktrees).
- Git worktree hygiene to prevent a specific silent-hang bug in some AI coding tools.
- The journal convention this skill plugs into.

The skill works without these. It just works better with them, because the canonical journal spec lives in the all-coding-projects `CLAUDE.md` and the skill defers to it.

## Why this exists

Two reasons.

One, I needed it. Vibe coding is fast enough that two weeks away from a project is the same as two months used to be, and my memory hasn't kept up.

Two, I'm sure other people have built better versions of this. Open an issue or a PR. I want to steal the good parts.

##License
MIT.
EOF
