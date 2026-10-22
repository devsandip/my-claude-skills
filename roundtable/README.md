# Roundtable

Get Claude, Codex (ChatGPT) and Gemini to work one problem together, argue in
rounds, and sign a single proposal. No APIs: everything runs on the apps and
CLIs you already have.

Two ways to run it, sharing the same rules of argument:

| | Desktop apps | Headless skill |
|---|---|---|
| How it starts | Paste one kickoff prompt into each app | Ask any one of the three coding apps to run `/roundtable` |
| Who drives it | Each app wakes itself on its own scheduler | A conductor script calls `claude -p`, `codex exec`, `gemini -p` |
| Context | Each app brings its own memory, files and connected accounts | Only what you write in the brief or pass as files and folders |
| Channel | A shared folder; each app writes only its own files | The script writes every file; the models only read |
| After each round | Each app posts its own summary in its chat and asks its questions | The starting app summarizes all three and asks the open questions |
| Good for | Problems that depend on what each app knows about you | Problems you can fully describe |

## Desktop apps

Files in `desktop/`:

- `PROTOCOL.md`: the rules every app follows (folder layout, status files,
  rounds, convergence, drafting, review, checksum sign-off, check-ins).
- `KICKOFF.md`: the prompt to paste, setup steps, and per-app notes on
  scheduling and folder access.

## Headless skill

`headless/roundtable/` is a skill in the open Agent Skills format. Install it
where each app looks for skills:

```bash
cp -R headless/roundtable ~/.agents/skills/roundtable   # Codex and Gemini CLI
cp -R headless/roundtable ~/.claude/skills/roundtable   # Claude Code
python3 ~/.agents/skills/roundtable/scripts/roundtable.py doctor
```

Try it without calling any model:

```bash
python3 ~/.agents/skills/roundtable/scripts/roundtable.py start --dry-run \
  --no-context --pause never --problem "Test" --home /tmp/roundtable-test
```

Then `doctor --live` sends each CLI a one-line prompt. Needs Python 3 and the
three CLIs signed in with your own accounts. The script strips API keys from
its environment, so all calls run on those logins.

## What the rules try to prevent

From a first manual run of this process:

- Agreement is not evidence. Positions change only for a source, a check, or
  a clear argument, and every claim about files or facts gets verified.
- Round 1 is blind, so the three start from independent views.
- The proposal must include a coverage check against the original brief.
  The first run missed nine items because nobody checked.
- Silence never counts as approval, and nobody signs for anyone else.
- Times come from the machine clock; models estimate them badly.
- Sign-off is on an exact version, recorded with a SHA-256 checksum.
