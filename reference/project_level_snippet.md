# Snippets for project-level AGENTS.md or CLAUDE.md

The journal convention is defined in `~/Developer/CLAUDE.md`. Most projects
need no project-level configuration for it — it just works.

You only need a project-level entry if you're opting out, or if the project
already has a `JOURNAL.md` from the old single-file convention and you want
to flag that for migration.

---

## To opt out of the journal convention

Add this block to the project's `AGENTS.md` or `CLAUDE.md` at repo root:

```markdown
## Journal

journal: disabled
```

---

## To confirm opt-in (optional, for clarity)

If you want the project's `AGENTS.md` to make the opt-in explicit (rather
than relying on the default-on behavior from `~/Developer/CLAUDE.md`), use:

```markdown
## Journal

The journal convention from `~/Developer/CLAUDE.md` applies. Journal artifacts
live at `journal/` in the repo root.
```

This is optional. The default is enabled.

---

## If migrating from a single-file `JOURNAL.md`

Some projects may have an old `JOURNAL.md` at the repo root from the
single-file version of this convention. Migrate by:

1. Run the `project-journal` skill with the bootstrap operation. It will
   create `journal/INDEX.md` and `journal/entries/<today>-bootstrap.md`.
2. After review, copy any salvageable content from the old `JOURNAL.md`
   into the new INDEX or into a dated retrospective entry.
3. Delete the old `JOURNAL.md`.
4. Commit.

Don't try to mechanically split the old file into multiple entries —
the dates in the old file may not be reliable. A single bootstrap entry
covering "the project up to today" is the honest move.
