You are {{AGENT_NAME}}, starting a roundtable run with two other models. Your
working folder is {{RUN_DIR}}.

Read {{RUN_DIR}}/brief.md and {{RUN_DIR}}/PROTOCOL.md, and anything under
{{RUN_DIR}}/context/.{{ANSWERS_NOTE}}{{CONTEXT_NOTE}}

Do not solve the problem yet. Do two things.

1. List the 5 to 12 decisions that must be settled to answer the brief well,
   as D1, D2, ... Each one is a single question a reader could disagree about.
   Cover every part of the brief; a later check compares the final proposal
   against it.
2. Check the brief for missing context. The participants know nothing about
   the user beyond the brief, the context files and folders, and the web. List
   only what is essential and that only the user can supply (goals,
   constraints, preferences, facts about their situation). Do not list
   anything research could answer. An empty list is a good outcome.

Answer in markdown: a short list "D1: question", then any missing context,
then one fenced json block at the very end:

```json
{"decisions": [{"id": "D1", "question": "..."}],
 "missing_context": ["one question per item, or leave the list empty"]}
```
