# Auto-Log Rule

After completing any task that changes files, append an entry to
`.ai-context/prompt_history.md` **without being asked**. The audit trail must exist without
depending on the engineer remembering to write it.

## Entry format

```markdown
### <YYYY-MM-DD HH:MM> — <slug>.<task-id or "n/a">
**Prompted by:** <engineer name>
**Instruction (summary):** <one line — what was asked, by ID>
**Artefacts touched:** <file paths>
**Outcome:** <what was generated/changed; tests run and result>
**Follow-up:** <open questions, or "none">
```

## Rules

- One entry per completed task, not per message.
- Reference work by identifier (`<slug>.T03`), never by re-describing the feature.
- **Never** record secrets, credentials, tokens, real customer data, or PII. If the instruction
  contained any, log the shape of it, not the value.
- If a task was abandoned or reverted, log that too — a missing entry looks like it never happened.
- `prompt_history.md` is the session-level audit trail. It is not a substitute for the
  human-curated daily summary in `.ai-context/status.md`; both get updated.

## Same-day status obligation

Alongside the prompt-history entry, update `.ai-context/status.md`:

- Move the task checkbox state in `.ai-context/tasks/<slug>.tasks.md`.
- Update the row for that spec in the Active Specs table.
- Add a line to the Daily Execution Log under today's date.

A stale `status.md` recreates the exact "ask around to find out what's in flight" problem the
artefact exists to remove.
