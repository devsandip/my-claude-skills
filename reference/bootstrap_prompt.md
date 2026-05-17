I've updated the journal convention in `~/Developer/CLAUDE.md`. The journal is now a directory (`journal/INDEX.md` + `journal/entries/*.md` + `journal/weekly/*.md`) instead of a single file. Re-read the "Project journal" section in `~/Developer/CLAUDE.md` now to pick up the full spec.

For this project, do the following:

1. Use the `project-journal` skill's **bootstrap** operation. Read `~/.claude/skills/project-journal/SKILL.md` and follow Operation 1 (Bootstrap) step by step.

2. If a single-file `JOURNAL.md` already exists at the repo root from the old convention, do not delete it yet — flag its existence to me and proceed with bootstrap. We'll handle migration after I review the new files.

3. Pay close attention to the rules: do not invent facts, do not auto-commit, do not fabricate a multi-entry history.

4. When you present the result to me, walk through what you put where, what you sourced each non-trivial claim from, and what's a placeholder.
