# Spec: \<Feature Name\>

> **Canonical template.** Every specification in this repository follows this structure. Amend
> this file only by explicit TL decision — do not silently drop, rename, or reorder mandatory
> sections.
>
> Under Data model changes and API contract, describe the intended (or, for a spec documenting
> already-shipped behaviour, the as-built) surface precisely. Gaps between as-built and desired
> behaviour go in Open questions or Risks, not silent rewrites.

## Spec ID
\<feature-slug\>

## Status
Draft v1.0
<!-- Draft → In Peer Review (Gate 1) → Changes Requested ⟲ → Approved → Plan Drafted →
     Plan Reviewed → Tasks Generated → In Development → In QA → Ready for Release →
     Released (vX.Y.Z) → [Deprecated / Superseded]. Nothing skips a state.
     On "Changes Requested", bump the marker (Draft v1.1) so revision history is visible here. -->

## Author / Reviewer
**Author:** \<name\> ·
**Gate 1 reviewer:** \<name — PM, per constitution.md roster; never the author\>

---

## 1. Business objective

\<One paragraph: what business outcome this delivers, for whom, and why it matters.
A zero-context engineer must understand the *why* before the *how*.\>

## 2. Scope / Out of scope

### In scope
- \<concrete capability or surface this spec owns\>

### Out of scope
- \<tempting adjacent work owned by another slug, or explicitly deferred\>
- \<link related specs by slug where known\>

## 3. Dependencies

| Kind | Reference | Why |
|---|---|---|
| Spec | \<slug or "none"\> | \<blocking or soft\> |
| BRD | \<BRD-NNN\> | \<requirement source\> |
| ADR | \<ADR-NNNN or "none"\> | \<infra or design decision\> |
| Runtime | \<WordPress hook/integration or "none"\> | \<failure mode if unavailable\> |
| Code | \<wp-content/plugins/... paths\> | \<modules this spec documents or changes\> |

## 4. Domain context

- **Plugin:** \`wp-content/plugins/\<slug\>/\`
- **Architecture:** [.ai-context/architecture.md](../architecture.md) \<section\>

\<Short paragraph on how this feature fits the portal — what it owns, what it must not own.\>

## 5. Actors / Roles

| Actor | Capability posture | What they do here |
|---|---|---|
| \<e.g. Requester (Employee)\> | \<logged-in employee\> | \<action\> |
| \<Unauthenticated caller\> | none | \<expected denial\> |

## 6. Functional requirements

| ID | Requirement |
|---|---|
| \`\<slug\>.FR01\` | \<testable statement — no adjectives\> |
| \`\<slug\>.FR02\` | … |

## 7. Non-functional requirements

| ID | Requirement | Source |
|---|---|---|
| \`\<slug\>.NFR01\` | Latency: p95 < 400 ms (constitution baseline) | constitution |
| \`\<slug\>.NFR02\` | Coverage floor on changed files: 80% | constitution |
| \`\<slug\>.NFR03\` | PII / logging / audit / rate-limit as applicable | constitution + this spec |

## 8. API contract

<!-- Omit the whole section only for a pure hook-driven side effect with no HTTP surface. Prefer
     "none — document why" over silence. Every route this spec owns must appear here. -->

### \`\<slug\>.API01\` — \<METHOD\> /wp-json/one-point/v1/\<path\>

**Auth:** \<capability required, e.g. \`current_user_can('read')\`\> ·
**Nonce:** \<required | not applicable\> ·
**Rate limit:** \<decision, or "none, because …"\>

**Request:**
\`\`\`json
{ "field": "type" }
\`\`\`

**Success (\<code\>):**
\`\`\`json
{ "field": "type" }
\`\`\`

**Exceptions:**

| HTTP | \`error.code\` | Condition |
|---|---|---|
| 400 | \`validation_error\` | … |
| 401 | \`unauthorized\` | … |
| 403 | \`forbidden_missing_capability\` | … |

## 9. Data model changes

| Table / column | Change | Migration (`dbDelta()`) |
|---|---|---|
| \`wp_\<table\>\` | \<new table / new column / none\> | \<required \| none\> |

## 10. Permissions / Security

| Concern | Rule for this spec |
|---|---|
| Capability required | \`current_user_can('\<capability\>')\` … |
| Nonce | \<which routes require it\> |
| PII | \<fields; masking; never log employee-identifying detail\> |
| Downstream dispatch | \<which `do_action()` hooks this spec fires or listens to\> |

## 11. Error scenarios

| ID | Trigger | Expected HTTP / \`error.code\` | Notes |
|---|---|---|---|
| \`\<slug\>.ERR01\` | … | … | … |

## 12. Acceptance criteria

1. \`\<slug\>.AC1\` — Given \<state\>, when \<action\>, then \<outcome\>.
2. \`\<slug\>.AC2\` — …

<!-- Every AC is given/when/then and individually IDed. No adjectives. -->

## 13. Test scenarios

| Test ID | Maps to | Layer | Scenario | Expected |
|---|---|---|---|---|
| \`\<slug\>.UT01\` | AC1 / FR01 | unit (Brain Monkey) | … | … |
| \`\<slug\>.IT01\` | AC2 | integration (\`WP_UnitTestCase\`) | … | … |

## 14. Performance

| Endpoint / operation class | p95 target |
|---|---|
| … | \< 400 ms (constitution baseline) |

## 15. Coverage

**Floor:** 80% on **changed files only** (constitution).

## 16. Risks

| ID | Risk | Mitigation / owning action |
|---|---|---|
| \`\<slug\>.R01\` | … | … |

## 17. Open questions

| ID | Question | Owner | Blocks Gate 1? |
|---|---|---|---|
| \`\<slug\>.Q01\` | … | … | Yes / No |

## 18. Traceability

| Artefact | Reference |
|---|---|
| BRD | \`BRD-NNN\` |
| ADR | \`ADR-NNNN\` or none |
| Related specs | \`\<slug\>\` … |
| Plan | \`.ai-context/plans/\<slug\>.plan.md\` (after Gate 1) |
| Tasks | \`.ai-context/tasks/\<slug\>.tasks.md\` (after plan) |
| Tests | \`tests/…\` · IDs in §13 |

## 19. Review checklist (Gate 1)

Reviewer confirms each box before **Approved**:

- [ ] Business objective is clear and matches the BRD citation
- [ ] Scope / out of scope prevents adjacent-slug creep
- [ ] Dependencies and blocking BRDs/ADRs are honest
- [ ] Every owned HTTP route has an API contract entry (or explicit "no HTTP")
- [ ] Acceptance criteria are given/when/then and IDed
- [ ] Error scenarios are exhaustive for the contract, not happy-path-only
- [ ] Performance and coverage expectations are named and match the constitution
- [ ] Capability, nonce, PII, and rate-limit decisions are explicit
- [ ] Risks reference \`status.md\` engineering risks where applicable
- [ ] Open questions that block Gate 1 are none — or escalated before approval
- [ ] Author ≠ Gate 1 reviewer
- [ ] Traceability table is complete

**Gate 1 decision:** \<Pending | Approved | Changes Requested\> · **Date:** \<YYYY-MM-DD\> · **Reviewer:** \<name\>
