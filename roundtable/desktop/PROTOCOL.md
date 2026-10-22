# Roundtable protocol (desktop apps)

Several AI apps (Claude, Codex in the ChatGPT app, Gemini, or others) work on
one problem in a shared folder, in rounds, and end with one proposal they all
sign. Nobody can message another app directly. The folder is the only channel
and the only memory: a check-in that starts in a fresh session must be able to
continue from the files alone.

## 1. Names and folder

Each participant uses its lowercase app name from the brief's participant
list (`claude`, `codex`, `gemini`, ...) for its folder and in every file.

```
<RUN>/
  .setup/            created with mkdir by the first to arrive; acts as a lock
  brief.md           the problem, participants, drafter, start time
  PROTOCOL.md        copy of this file
  DECISIONS.md       decisions to settle, D1, D2, ... (written by the framer)
  ANSWERS.md         the user's answers; only the user writes it
  <name>/
    STATUS.md        your progress signal; only you write it
    NOTES.md         your working memory between check-ins
    round-N.md       your round answers
    QUESTIONS.md     questions only the user can answer
    user-answers.md  the user's replies in your chat, copied word for word
    summary-round-N.md  what you told the user when round N closed
    messages.md      append-only notes to the others, newest at the bottom
    review-vN.md     your review of proposal version N
  consensus/         written only by the drafter
    PROPOSAL-vN.md
    FINAL.md
```

## 2. Write rules

- Write only inside your own folder. The exceptions: the first to arrive
  writes `brief.md` and `PROTOCOL.md`; the framer writes `DECISIONS.md`; the
  drafter writes `consensus/`. Never overwrite a file someone else wrote.
- A file is frozen once your STATUS says that phase is `complete`.
  Corrections go in your next file.
- Take every time from the machine clock (run `date`). Never estimate a time.
- Do not touch anything outside the run folder unless the brief lists it as
  context, and then only read it.

## 3. STATUS.md

```
agent: claude
phase: setup | round-1 | round-2 | round-3 | round-4 | draft | review-v1 | ... | done
state: working | complete | waiting
updated: 2026-10-21 14:05 IST
note: one line on what you are doing or waiting for
```

Update `updated` on every check-in, even when nothing else changed. It is the
others' only sign that you are alive.

## 4. Phases

**Setup.** Create your folder, `STATUS.md` and `NOTES.md`. The framer is the
participant whose name comes first alphabetically. The framer writes
`DECISIONS.md`: the 5 to 12 decisions that must be settled to answer the
brief, as D1, D2, ..., covering every part of it. Everyone else waits for
`DECISIONS.md`.

**Round 1, blind.** Write `round-1.md` in the format in section 8. Do not
open another participant's folder until every participant's STATUS shows
`round-1` `complete`.

**Rounds 2 to 4.** Start round N only when every participant shows round N-1
complete. Read every other participant's latest round in full first. Write
`round-N.md`.

**Convergence.** After round N (N of 2 or more): if every participant's
verdict says `converged: true` with no `open_disagreements`, go to drafting.
Otherwise start the next round. After round 4, draft anyway and mark split
decisions "Unresolved".

**Draft.** The drafter is the one named in the brief, or else the framer.
The drafter writes `consensus/PROPOSAL-v1.md`:

1. The answer, in one paragraph.
2. Decisions: the agreed position per ID; "Unresolved" rows give each
   participant's position in a line.
3. The plan or recommendation, in enough detail to act on.
4. What was considered and rejected, with the reason.
5. Coverage check: every item in `brief.md` and every concern any participant
   raised in any round, with where the proposal covers it, or "Not covered"
   and why. Do not skip this.
6. Questions for the user.
7. Sources.
8. Sign-off: an empty list of participants.

**Review.** Each other participant writes `review-vN.md`: a short review
checking that the proposal states what was agreed, that its facts are right,
that coverage is complete, and that no step is promised in an order that
cannot work. End with:

```json
{"agent": "codex", "version": 1, "sign": true,
 "amendments": [{"section": "...", "change": "...", "reason": "..."}]}
```

Sign only if you would defend the text as written. If anything changes the
meaning, `sign` is false.

**Revise.** If any review asks for amendments or does not sign, the drafter
writes the next version, applying what the evidence supports and listing
rejected amendments with reasons. Stop at version 3; whatever is still
disputed is recorded in FINAL.md.

**Final.** When every reviewer has signed the same version, the drafter
writes `consensus/FINAL.md`: the version, who signed and when, and the
SHA-256 of that proposal file (`shasum -a 256 <file>`). Each participant
recomputes the hash, sets STATUS to `done`, cancels its own scheduled
check-ins, and tells the user in its own chat where the proposal is.

## 5. Check-ins

A check-in is one wake-up: scheduled, or the user typing "continue". Each one:

1. Run `date`. Read every participant's STATUS.md, your own NOTES.md,
   `ANSWERS.md`, every participant's `user-answers.md`, and anything new at
   the bottom of the others' messages.md.
2. If a round has closed (every participant shows it complete) and your
   NOTES.md does not say you reported it, report it now (section 6).
3. Work out your next step from section 4. If you can take it, do it fully
   now, then update STATUS.
4. If you are waiting on someone, refresh your STATUS `updated` time and stop.
   Do not wait inside a check-in for more than two minutes.
5. Update NOTES.md: current phase, what you did, what you are waiting for,
   which rounds you have reported, how to cancel your schedule, anything a
   fresh session would need.
6. Stalled peer: if a participant you are waiting on has not refreshed STATUS
   for 60 minutes, say so in your chat to the user and in your messages.md.
   Keep waiting. Never continue without a participant unless the user writes
   that in ANSWERS.md.
7. Time limit: 8 hours after `brief.md` was written, stop your schedule and
   tell the user where things stand.

## 6. Reporting to the user after each round

A round closes when every participant's STATUS shows it complete. At your
first check-in after that, before you start the next round, read every
participant's file for that round. Then write `summary-round-N.md` in your
folder and post the same text in your chat:

1. **Round N, and what happens next.**
2. **What I did:** one or two lines on my position and the evidence I added.
3. **Where I agree with the others:** decision, the shared position, and who
   shares it.
4. **Where I disagree:** decision, my position against theirs, and why.
5. **What changed:** who moved on what this round, including me, and the
   evidence that did it.
6. **Questions for you:** see section 7. Write "None" if there are none.

Keep it short enough to read on a phone. Record in NOTES.md that round N is
reported, so you report it once. After round 1 this is the first moment
anyone sees the others' positions, so the agree and disagree parts start
there. Report the same way when the proposal is final: the answer, who
signed, anything unresolved, and the path to `consensus/PROPOSAL.md`.

## 7. Questions for the user

- Ask in your chat, inside your round summary, or straight away if something
  blocks a decision.
- Before asking, read the other participants' QUESTIONS.md and
  user-answers.md. Do not ask what someone already asked or the user already
  answered. If another participant asked it, say so and ask the user to
  answer once, in either chat.
- Write each question you ask in your QUESTIONS.md.
- When the user replies in your chat, copy their words exactly, with the time
  and the question they answer, into your `user-answers.md`. That is how the
  other apps see the answer. The user may also write in `ANSWERS.md` directly.
- Answers from any participant's user-answers.md or from ANSWERS.md override
  assumptions for everyone.
- Do not stop work to wait for an answer unless the decision is impossible
  without it. Carry on with your best assumption and state it.

## 8. Round answer format

Markdown with these sections, then one fenced json block at the very end:

1. Recommendation in one paragraph
2. Decision table: one row per ID with position, confidence, one-line reason
3. What changed since my last round, and why (round 2 on)
4. Pushback on the others (round 2 on); in round 2, name the weakest point
   in each other participant's round 1
5. Questions for the other participants
6. Assumptions I made
7. Sources

```json
{"agent": "claude", "round": 1,
 "positions": [{"id": "D1", "position": "one line", "confidence": "high | med | low"}],
 "new_decisions": [{"id": "C1", "question": "one line"}],
 "open_disagreements": ["D3"],
 "questions_for_user": [],
 "converged": false}
```

Missing decisions can be added with your initial as prefix (C1, X1, G1); list
them in `new_decisions` and the others take a position next round.

## 9. Rules of argument

1. Change a position only for evidence: a source, a check you ran, or a clear
   argument. Say which. Do not agree to be agreeable.
2. Verify any claim about files, code or facts before relying on it, including
   claims made by another participant.
3. Facts that can change (prices, versions, policies, availability) need a
   dated source. Mark what you could not verify.
4. Cite the file and section you are answering.
5. Silence is never agreement and never approval.
6. Style for anything the user reads: short plain sentences, lead with the
   answer, no em dashes, no emojis, no filler adjectives.
