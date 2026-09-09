# Tasks: <Feature Name>

## Derived From
.ai-context/plans/<feature-slug>.plan.md (Status must be **Plan Reviewed**)

## Task states
`[ ]` Not Started · `[~]` In Progress · `[?]` In Review · `[x]` Merged

## Sequence

- [ ] `<slug>.T01` — <independently verifiable unit of work> — Acceptance: `<slug>.AC1`, `<slug>.AC3`
- [ ] `<slug>.T02` — <...> — Acceptance: `<slug>.AC2`, `<slug>.API01`
- [ ] `<slug>.T03` — <e.g. wire `do_action()` dispatch to downstream coordinator> — Acceptance: `<slug>.API01`

## Rules

- The engineer runs the agent against **one task at a time**, never the whole file in one prompt.
- Each task is independently generatable, reviewable, and mergeable.
- Tests for a task are generated from the spec's test-case table and confirmed **Red** before the
  implementation task starts.
- Prompt by identity: "Implement `<slug>.T03` — must satisfy `<slug>.AC3` and match the exception
  table in `<slug>.API01`. Do not touch T01/T02, already merged."
- Update the checkbox and `status.md` the same day a state changes.

## Red confirmation log

| Task | Tests written | Confirmed Red | Green | Merged |
|---|---|---|---|---|
| `<slug>.T01` | | | | |
