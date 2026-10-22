---
name: roundtable
description: Run a roundtable between Claude, Codex (ChatGPT) and Gemini through their CLIs on this Mac. They debate in rounds and end with one proposal all three sign. Use when the user says /roundtable, or asks to get all three models (or "the other AIs") to debate, cross-check, deliberate or reach consensus on a problem whose context can be written down.
---

# Roundtable (headless)

Three models work on one problem in rounds and end with a proposal they all
sign. You do not take part in the debate. You write the brief, start the
conductor script, relay questions to the user, and report the result.

The script calls `claude -p`, `codex exec` and `gemini -p` headless and
read-only, writes every file in a run folder under `~/Roundtable/`, and strips
API keys so all three run on the user's subscriptions.

Script: `scripts/roundtable.py` in this skill's folder (Python 3, standard
library only). Below, `RT` means its absolute path.

## The one thing to get right: context

The headless participants do not have what you have. They cannot see the
user's memory, this conversation, or the apps connected to you (mail,
calendar, Notion and so on). They do read the user's global instruction files
for each CLI (`~/.claude/CLAUDE.md`, `~/.codex/AGENTS.md`,
`~/.gemini/GEMINI.md`), the brief, the context you pass, and the web.

So your main job is to write a self-contained brief:

1. If the user did not give you any context files or folders, ask before
   anything else: "Are there documents, notes or folders the other models
   should read? Anything about your situation they need to know?" Wait for
   the answer. The script refuses to start without `--context`, unless you
   pass `--no-context` after the user confirms there is nothing to add.
2. Fill in `templates/brief-template.md` from what you know: the user's
   words, relevant facts from your memory of them, and anything from this
   conversation that changes the answer. Leave out personal details the task
   does not need.
3. Pass documents with `--context FILE` (copied into the run so everyone
   reads the same version) or `--context FOLDER` (read in place). Read-only
   either way.
4. If something essential is still missing that only the user knows, ask now.
   Do not ask about anything the participants can research.
5. Show the user the brief in a few lines before starting.

As a backstop, the framing step lists any essential context still missing; if
it finds some, the run pauses before round 1 and asks the user.

If the task depends on things that cannot be written down (live access to the
user's accounts, long private history), say so: this mode is the wrong tool,
and the desktop roundtable, where each app keeps its own context, fits better.

## Start the run

```bash
python3 "$RT" start --from <claude|codex|gemini> --brief - \
  --context ~/path/to/notes.md --context ~/Developer/some-project <<'EOF'
<the brief>
EOF
```

With no context files, after the user confirmed: replace the `--context`
flags with `--no-context`.

`--from` is the app you are; it also frames the decisions to settle. The first
output line is `RUN_DIR=...`; keep it.

A run takes about 30 to 90 minutes. Start it in the background and check with
`python3 "$RT" status <RUN_DIR>` every few minutes; never block on it.

- Claude Code: run the command as a background Bash command.
- Codex: its sandbox blocks network, and the script needs it to reach the
  other two CLIs. Ask to run this one command with escalated permissions and
  let the user approve it.
- Gemini CLI: `nohup python3 "$RT" start ... > ~/Roundtable/last.log 2>&1 &`,
  then poll status.

Options: `--rounds N` (default 4, hard stop), `--pause every-round |
questions-only | never` (default every-round: stop after each round so you can
report; use questions-only or never if the user wants it to run unattended),
`--no-context`, `--drafter`, `--only claude,gemini`, `--dry-run` (no models
called; exercises the whole flow).

## After every round: report to the user

By default the script stops after each round. It writes a digest at
`<RUN_DIR>/summaries/round-N.md`: a table of each participant's position on
every decision, which decisions are still disputed and by whom, and the
questions for the user. Read the digest and the three round files, then tell
the user, briefly:

1. **Round N of M, and what happens next.**
2. **Agreed:** decisions where all three now hold the same position, one line
   each.
3. **Still split:** each disputed decision, who holds what, and the main
   argument on each side.
4. **What changed:** who moved on what, and the evidence that moved them.
5. **Open questions for you:** the participants' questions, deduplicated,
   each with who asked. Ask them directly and wait for the answers.

Write in your own words from the files; do not paste the digest. Keep it
short enough to read on a phone.

## Exit codes

- `4` paused after a round, no questions: give the round report, then run
  `python3 "$RT" continue <RUN_DIR>` in the background.
- `3` paused with questions (before round 1 for missing context, or after a
  round): give the round report if there is one, ask the questions in
  `<RUN_DIR>/QUESTIONS-FOR-YOU.md`, and wait. Append the user's answers, in
  their words, to `<RUN_DIR>/ANSWERS.md` under a heading such as
  "After round 2", then run `continue`. If the user declines to answer, run
  `continue <RUN_DIR> --skip-questions`; the participants proceed on their
  stated assumptions.
- `2` usage: most often no context was given. Ask the user for context (step
  1 above) and start again.
- `1` error: read the end of `<RUN_DIR>/log.md` and the failing participant's
  `raw/` folder. Usual causes: a CLI is logged out, a timeout
  (`ROUNDTABLE_TIMEOUT_SEC`), or the script ran inside a sandbox without
  network. Fix it and run `continue`; finished work is kept.
- `0` done.

## Report the result

Read `<RUN_DIR>/consensus/FINAL.md` and `PROPOSAL.md`, and skim each
participant's last round. Tell the user briefly:

- the answer, in two or three sentences;
- whether all three signed, and anything unresolved;
- where they disagreed and what changed minds;
- anything the coverage check marks "Not covered";
- the path to `PROPOSAL.md`.

## Rules

- Never edit a participant's round file, a review or the proposal.
- `ANSWERS.md` holds only the user's words, never your own judgment.
- Do not sign or approve on the user's behalf.
- No API keys and no `--bare`; the user wants subscription logins only.

## Checks

`python3 "$RT" doctor` lists the three CLIs it found; `doctor --live` also
sends each a one-line prompt. Override binaries with `ROUNDTABLE_CLAUDE_BIN`,
`ROUNDTABLE_CODEX_BIN`, `ROUNDTABLE_GEMINI_BIN`, and add flags with
`ROUNDTABLE_CODEX_ARGS` and the like.
