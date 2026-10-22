# Roundtable kickoff (desktop apps)

Paste the same prompt into each app that should take part. Fill in the four
lines at the top first. Each app works out which participant it is, joins the
shared folder, schedules its own check-ins, and runs until all participants
sign one proposal.

## Before the first run

1. Make one folder every app can read and write, and put the protocol in it:
   `mkdir -p ~/Roundtable && cp PROTOCOL.md ~/Roundtable/PROTOCOL.md`
2. Give each app access to `~/Roundtable` (see "Per-app notes").
3. Keep the computer awake for the run, for example `caffeinate -dis` in a
   terminal.

## The prompt

```
ROUNDTABLE KICKOFF

Run folder:   ~/Roundtable/2026-10-21-short-topic
Participants: claude, codex, gemini
Drafter:      claude
Check-in:     every 10 minutes

Problem:
<<<
Describe the problem here: what needs deciding or producing, why it matters,
constraints, what is already decided, and what output you want. Name any
files or folders the participants should read.
>>>

Instructions:
1. Work out which participant you are from the list above and use that
   lowercase name everywhere.
2. Read ~/Roundtable/PROTOCOL.md in full and follow it exactly.
3. Setup: run `mkdir <run folder>/.setup`. If that succeeds, you are first:
   write <run folder>/brief.md (the problem above, the participant list, the
   drafter, and the time from `date`) and copy ~/Roundtable/PROTOCOL.md into
   the run folder. If it fails, someone else is setting up; never overwrite
   brief.md or PROTOCOL.md. Then create your own folder, STATUS.md and
   NOTES.md.
4. Schedule your own check-ins with your app's built-in scheduler at the
   interval above, coming back to this conversation if your app allows it.
   Use this as the scheduled prompt: "Roundtable check-in for <run folder>
   as <your name>: follow section 5 of <run folder>/PROTOCOL.md." Write in
   NOTES.md which scheduler you used and how to cancel it.
5. Do your first check-in now, then end your turn. Do not wait in a loop.
6. You cannot message the other apps. Everything they should see goes in
   files in your folder, as the protocol says.
7. Bring what you know about me and this problem into your rounds where it
   helps: you may have memory, files or connected apps the others lack. Say
   what you used.
8. When each round closes, post a summary here as section 6 of the protocol
   describes: what you did, where you agree with the others, where you
   disagree and why, what changed, and any questions for me. When I answer in
   this chat, copy my words into your user-answers.md so the others see them.
9. Otherwise talk to me only when something is stuck or the roundtable is
   done (give the path to consensus/PROPOSAL.md).
```

To stop a run early, tell each app "stop the roundtable and cancel your
check-ins".

You will get one round summary from each app per round. Answer a question in
whichever chat asked it; each app copies your answer into its folder so the
others see it. If your app's scheduled check-ins open a new chat each time
instead of returning to this one, answer in the newest one, or write in
`ANSWERS.md` in the run folder.

## Per-app notes

Scheduling and folder access differ by app. What matters is that each app can
read and write the run folder and wake itself on a timer.

- **Claude Code**: start it in `~/Roundtable` (or add the folder), then let it
  use `/loop 10m <check-in prompt>`, which returns to the same session.
- **Claude desktop app**: connect `~/Roundtable` as a folder. Ask it to
  schedule check-ins that come back to this conversation. Its scheduled tasks
  run in the cloud and can only reach your files while the desktop app is
  open and connected to this computer.
- **Codex in the ChatGPT desktop app**: open `~/Roundtable` as the project, with
  a sandbox that allows writing in it. Ask it to create a scheduled task
  inside this chat so each run returns here. Keep the app running.
- **Gemini in Antigravity**: open `~/Roundtable` as the workspace and use
  `/schedule` with a 10-minute cron and a cap on runs. Schedules belong to the
  conversation and wake it with its history.
- **Gemini CLI**: has no scheduler. Type "continue" when you want it to check
  in, or let it poll in a shell loop for up to an hour per turn.
- **Any other app**: works if it can read and write the folder. Without a
  timer, nudge it with "continue".

## When to use the headless skill instead

Use the `roundtable` skill in `headless/` when you can write the context down
and want it to run unattended from one app with one prompt. Use this desktop
version when each app's own memory, files and connected accounts matter to
the answer.
