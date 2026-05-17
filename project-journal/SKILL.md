---
name: project-journal
description: Bootstrap, write weekly summaries for, or substantively refresh a project's journal directory (`journal/INDEX.md` + `journal/entries/` + `journal/weekly/`) — the WBR-style narrative record of a project's evolution. Use this skill whenever the user says "set up the journal," "bootstrap the journal," "create the journal for this project," "write the weekly summary," "do the weekly rollup," "refresh the index," "the journal index is stale," or any variant asking to create or substantially rework the journal artifacts. Also use when the user references the journal convention in `~/Developer/CLAUDE.md` and wants to apply it to a new or existing project. Do NOT use this skill for routine single-entry daily journal additions during normal work — those are handled by the ambient convention in `~/Developer/CLAUDE.md`. Single entries don't need a skill; the skill is for the heavier operations (bootstrap, weekly synthesis, index refresh).
---

# Project Journal

A skill for the substantive operations on a project's `journal/` directory: bootstrap, weekly summary writing, and INDEX refresh.

## The journal system in 30 seconds

The journal lives at `<repo-root>/journal/` and has three artifact types:

- **`INDEX.md`** — live, mutable, always reflects today. The aggregative view.
- **`entries/YYYY-MM-DD-HHMM-slug.md`** — daily/intra-day entries, frozen once written, backward-linked-list via "Previous" pointer.
- **`weekly/YYYY-Www-summary.md`** — weekly aggregations of that week's daily entries, frozen once written, backward-linked-list to previous week.

The full spec — purpose of each artifact, voice rules, when each one gets written — lives in `~/Developer/CLAUDE.md` under "Project journal — `journal/` directory". **Read that section first before doing any journal work.** This skill is procedural; that file is canonical.

## Operations this skill handles

1. **Bootstrap** — create the `journal/` directory and its initial INDEX + first entry on a project that doesn't have one.
2. **Write a weekly summary** — synthesize a week's worth of daily entries into a `weekly/YYYY-Www-summary.md` file.
3. **Refresh INDEX** — substantially rework `INDEX.md` when it's gone stale.

What this skill does NOT do:
- Single daily entry additions during normal coding work. The ambient convention in `~/Developer/CLAUDE.md` handles that. The user shouldn't invoke a skill to write one entry.
- Reading the journal at session start. Ambient.
- Multiple operations bundled. Pick one at a time.

---

## Operation 1: Bootstrap

When the user asks to set up the journal on a project that doesn't have one yet.

### Step 1: Verify

- Read `~/Developer/CLAUDE.md` "Project journal" section if you haven't already this session.
- Check the project's `AGENTS.md` and `CLAUDE.md` (at repo root) for `journal: disabled`. If present, stop and tell the user.
- Check whether `journal/` already exists at the repo root. If it does, ask whether the user wants the **refresh** workflow instead — bootstrap will create new files alongside existing ones, which is messy.

### Step 2: Gather raw material

Read enough of the repo to reconstruct an honest first entry. In rough order of value:

- `README.md` — usually gives the elevator and the "what this is."
- `package.json` / `pyproject.toml` / equivalent — stack, project name.
- `git log --oneline -100` — chronology of meaningful moves.
- `git log --since="3 months ago" --pretty=format:"%h %ad %s" --date=short` — recent activity in date order.
- `WORKLOG.md` if it exists — pre-digested session history.
- `/docs/prd.md`, `/docs/architecture.md` if they exist — declared truth.
- `AGENTS.md` / `CLAUDE.md` at repo root — project conventions.

Don't run heavy analysis on the codebase. The journal is a narrative summary, not an audit.

### Step 3: Create the structure

```bash
mkdir -p journal/entries journal/weekly
```

### Step 4: Write the first entry

File: `journal/entries/<today-date>-<current-time>-bootstrap.md`

Filename format: `YYYY-MM-DD-HHMM-bootstrap.md`. Use the actual current date and time.

The entry covers the project's arc up to today, based on what the repo shows. Use the daily entry template (in `~/Developer/CLAUDE.md`'s journal section, or fall back to the structure: What happened / Where we are now / What I believe / Open questions). **No "Previous" pointer** — this is the first entry.

Critical rules:

- **Do not invent facts.** If you can't source a claim from the repo, mark it as a placeholder: `<verify: ...>`. The user will fill these in.
- One entry is the right amount. Don't fabricate a series of historical entries just because the project is old.
- Write in first person, declarative, no em-dashes, no AI-assistant filler.
- Match the user's existing voice if there's any prior writing (README tone, blog post links, etc.).

### Step 5: Write the INDEX

File: `journal/INDEX.md`

Use the INDEX structure from `~/Developer/CLAUDE.md`:

- Last refreshed: today's date/time.
- Latest entry: the bootstrap entry you just wrote.
- One-line elevator: from README if you can extract it; otherwise placeholder.
- Where we are now: 2-4 short paragraphs of current state, sourced from the same material as the entry.
- Recent entries: just the bootstrap entry.
- Weekly summaries: empty for now.
- Working hypotheses / Open questions / Things ruled out: populate only from evidence in the repo; otherwise placeholders.

### Step 6: Present for review

Show the user both files. Walk through:

- What you put in each section.
- What you sourced each non-trivial claim from.
- What you left as placeholders, and why.

Ask: "Does this match how you'd describe the project? Anything to rewrite before I commit?"

**Do not commit.** The user reviews and commits.

---

## Operation 2: Write a weekly summary

When the user asks for a weekly summary, or when the start-of-Monday auto-offer fires.

### Step 1: Identify the week to summarize

- Default: the most recent ISO week that has daily entries but no weekly summary file yet.
- If the user names a different week ("summarize last week," "do W19"), use that.
- The filename will be `journal/weekly/YYYY-Www-summary.md`.

If the target week file already exists, stop and ask whether the user wants to overwrite it (which breaks the immutability rule and should be a deliberate exception).

### Step 2: Read every daily entry from that week

List the daily entries: `ls journal/entries/` and filter to that week's date range. Read each one fully. Do not skim — the synthesis depends on understanding the through-line.

### Step 3: Identify the previous weekly summary

Find the most recent weekly summary file before this week, if any. Its filename goes in the "Previous week" pointer. If this is the first weekly summary, omit the pointer.

### Step 4: Write the synthesis

Use the weekly template structure: The week in one paragraph / What happened / State at end of week / Beliefs that changed / Carry-overs / Daily entries from this week.

Critical rules:

- **Synthesize, don't concatenate.** Don't just list what each daily entry said. Find the through-line. What was the shape of the week?
- The "one paragraph" section is load-bearing. It's the thing a reader takes away if they read nothing else. Write it last, after you've drafted the rest, so it's a real summary.
- "Beliefs that changed" requires comparing this week's daily entries to previous belief state. If the prior weekly summary's "State at end of week" is materially different from this week's, name the changes.
- Voice: first person, declarative, short sentences, no em-dashes.

### Step 5: Update INDEX.md

The weekly summary doesn't fully replace INDEX work, but it does require an INDEX update:

- Prepend the new weekly summary to INDEX's "Weekly summaries" list.
- Refresh the "Last refreshed" timestamp.
- If the week's synthesis reveals that INDEX's "Where we are now" is stale, flag this to the user and offer to refresh INDEX as a follow-up operation (don't bundle).

### Step 6: Present and review

Same review/sign-off step as bootstrap. Do not commit.

---

## Operation 3: Refresh INDEX

When the user says INDEX is stale, or when you notice it's drifted significantly from current state.

### Step 1: Diagnose

Read INDEX.md end-to-end. Read the most recent 3-5 daily entries and the latest weekly summary. Categorize what's stale:

- "Where we are now" no longer matches reality. Most common.
- Working hypotheses list is out of date — items that have been confirmed/killed in recent entries weren't removed.
- Open questions list has items that recent entries answered.
- Things ruled out list is missing items that recent entries explicitly ruled out.

### Step 2: Propose changes before applying

Refresh is more invasive than bootstrap. Show the diff before applying.

- For each section that needs change: show old vs proposed new.
- Wait for explicit user yes before writing.

### Step 3: Apply

Write the updated INDEX.md. Update "Last refreshed" timestamp. Do **not** touch entries/ or weekly/ — those are frozen.

### Step 4: Present

Show the user the final INDEX. Same review/sign-off pattern.

---

## Voice and style (applies to all operations)

- First person, declarative, short sentences.
- No em-dashes. Use commas, parens, or separate sentences.
- No emojis.
- No "production-ready," "robust," "comprehensive," "seamlessly," "leverage."
- Match the user's existing voice if there are prior entries or writing.

## What to never do

- Never invent project history. If commits don't show a pivot, don't write a pivot.
- Never auto-commit. The user reviews.
- Never modify an existing daily entry or weekly summary silently. They're frozen.
- Never overwrite an existing INDEX.md without showing the diff first.
- Never bundle operations. Bootstrap, weekly summary, and refresh are separate, even if all three feel needed — do them one at a time with sign-off between.
- Never let a weekly summary be just a concatenation of daily entries. Synthesize or don't write it.
- Never use this skill for a single new daily entry during normal work. That's ambient, not skilled.
