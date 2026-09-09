# Workflow: /generate-plan

Use after a spec reaches **Approved** at Gate 1. Never before — a plan derived from an unapproved
spec inherits whatever ambiguity Gate 1 would have caught.

## Prompt template

```
Generate the plan for <slug>.

Read, in this order:
- .ai-context/specs/<slug>.spec.md          (the approved spec — the contract)
- .ai-context/constitution.md               (non-negotiables you must check against)
- .ai-context/architecture.md               (<relevant section only>)
- <the specific existing plugin files this feature touches, if any>

Produce .ai-context/plans/<slug>.plan.md using .ai-context/templates/plan.template.md.

Requirements:
- Name integration points and the data model (custom table / dbDelta migration) explicitly. Do
  not leave anything for implementation-time inference.
- Complete the Constitution Check section with a real verdict per line, not a blanket tick.
- State what you are explicitly deferring, with the reason.
- Sequencing must be an ordered list of independently verifiable units of work.
- If the spec is ambiguous anywhere, stop and list the questions instead of guessing.
```

## Rules

- Scope the context. Tag the plugin files the feature touches, never the whole repo.
- A plan that is silent on a constitution rule is a gap, not a neutral omission — the reviewer
  will treat silence as a question.
- If a decision would cost more than a day of rework to reverse, it needs an ADR in
  `.ai-context/decisions/`, not just a line in the plan.
- New datastore, queue, or scheduling mechanism: not permitted without an approved ADR. Async
  downstream dispatch goes through WordPress Action Hooks (`do_action`) only.

## After generation

1. Engineer reviews the plan against the spec line by line.
2. Attach the plan to Gate 1 for the plan-review pass.
3. On approval, move the spec status to `Plan Reviewed` and run `/generate-tasks` logic from the
   plan's Sequencing section.
4. Update `.ai-context/status.md` the same day.
