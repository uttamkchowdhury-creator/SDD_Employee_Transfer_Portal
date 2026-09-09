# Prompt History

Session-level agent audit trail, appended after every completed task per
`.agent/rules/auto-log.md`. Distinct from the human-curated daily summary in
[status.md](status.md) — both are maintained.

**Never** record secrets, credentials, tokens, real customer data, or PII. If an instruction
contained any, log its shape, not its value.

> **Reset notice (2026-08-29).** All prior entries in this file recorded work on an unrelated
> Node.js/TypeScript project (Empty Floor + Circle Tap) that this workspace was cloned from. That
> history has been cleared — it does not describe anything present in this repository going
> forward. The audit trail restarts below for the **One-Point Employee Portal**.

---

### 2026-08-29 — emp-internal-transfer.discovery-day-1

**Prompted by:** Developer (shamik.bhattacharya@intglobal.com)
**Instruction (summary):** Execute Day 1 Discovery & Requirement Analysis for the Employee
Internal Transfer Journey (`emp-internal-transfer`) on the One-Point Employee Portal (WordPress
plugin stack). Reset all previous-project SDD context (Empty Floor + Circle Tap, Node.js/TS) and
populate fresh `.ai-context/` and `.agent/rules/` content strictly for this feature: clear prior
`specs/`/`plans/`/`tasks/`/`test_cases/`/`decisions/` artefacts; author
`int-standards.wordpress.md`; rewrite `constitution.md`, `project_context.md`, `status.md`; author
`BRD-001` in `BRD.md`. No specs, plans, or code — Discovery only.
**Artefacts touched:**
`.ai-context/specs/*.spec.md` (11 removed), `.ai-context/plans/*.plan.md` (1 removed),
`.ai-context/tasks/*.tasks.md` (1 removed), `.ai-context/test_cases/*.md` (2 removed),
`.ai-context/specs/.gitkeep`, `.ai-context/plans/.gitkeep`, `.ai-context/tasks/.gitkeep`,
`.ai-context/test_cases/.gitkeep` (all new), `.agent/rules/int-standards.wordpress.md` (new),
`.ai-context/constitution.md`, `.ai-context/project_context.md`, `.ai-context/BRD.md`,
`.ai-context/status.md`, `.ai-context/prompt_history.md`
**Outcome:** Prior project's 11 specs, 1 plan, 1 tasks file, and 2 test-case files removed;
`.gitkeep` placeholders restored (`decisions/` and `releases/` were already empty and untouched).
New WordPress/PHP 8.x standards file authored covering plugin structure, `WP_REST_Controller`
convention, `$wpdb`/`dbDelta()` data-layer rules, capability/nonce/sanitization/escaping/
prepared-SQL security rules, zero-PII-in-logs rule, and PHPUnit + Brain Monkey test discipline
with an 80% coverage floor. `constitution.md` fully rewritten for the new stack — Testing
Discipline, Security Posture, Architectural Constraints, and Non-Functional Baselines sections
populated per instruction; governance roster (TL, PM) marked `[Open]`, no names given yet.
`project_context.md` fully rewritten — defines the portal's objective and role as the centralized
self-service gateway for internal transfers, and explicitly flags `architecture.md`, `SRS.md`,
`inventory/`, and `source-docs/` as stale prior-project content left untouched (out of scope for
this cleanup instruction). `BRD.md` reset and reseeded with **BRD-001: Employee Internal Transfer
Digital Journey** — business problem/objective, five named actor roles, the six-stage journey
(Request Initiation → Current Manager Approval → HR Eligibility Validation → Receiving Manager
Acceptance → Downstream Provisioning & Updates → Completion), a Business-vs-Technical decision
split (6-month tenure rule, sequential approval hierarchy, single-active-transfer limit vs.
plugin path, REST namespace, custom table, hook-based dispatch), two open ambiguities (5-day SLA
with reminders; domestic-only v1 scope) recorded as blocking items, and two explicit out-of-scope
items (cross-border tax compliance, inter-entity legal contract changes). `status.md` fully reset
— prior project's 96-spec retro-back-fill programme and risk register retired; new board tracks
only `emp-internal-transfer` in **In Discovery** status, with today's Daily Execution Log entry.
No source code, `.spec.md`, or `.plan.md` files were written, per explicit instruction. No tests
run — none exist yet.
**Follow-up:** BRD-001's two open ambiguities (SLA/reminders, domestic-only scope) need business
confirmation. A Technical Lead and Project Manager still need to be named on the constitution's
roster before any future spec can clear Gate 1/Gate 2. `architecture.md`, `SRS.md`, `inventory/`,
and `source-docs/` remain stale from the prior project and were intentionally left untouched — a
future session should either rebuild them for this feature or formally retire them. Do not author
`specs/emp-internal-transfer.spec.md` until explicitly prompted to do so.

### 2026-08-29 — emp-internal-transfer.discovery-day-1-purge-round-2

**Prompted by:** Developer (shamik.bhattacharya@intglobal.com)
**Instruction (summary):** "Remove all everything of old project replace with my project" — a
follow-up after confirming `source-docs/` still held the previous project's PDFs. Extend the Day 1
cleanup beyond the original five artefact directories to every remaining Empty Floor + Circle Tap
remnant in this workspace.
**Artefacts touched:** `.ai-context/SRS.md` (deleted), `.ai-context/source-docs/*.pdf` (4 deleted),
`.ai-context/source-docs/proposal-extract.md` (deleted), `.ai-context/source-docs/README.md`
(rewritten), `.ai-context/inventory/*.md` (4 deleted) + `.ai-context/inventory/.gitkeep` (new),
`.ai-context/architecture.md` (rewritten), `.agent/rules/int-standards.node.md` (deleted),
`.agent/rules/.agentignore` (rewritten), `.agent/workflows/code-review.md`,
`.agent/workflows/generate-plan.md`, `.agent/workflows/generate-tests.md`,
`.agent/workflows/gate-2-reviewer.md` (all rewritten),
`.agent/workflows/Gate-2-Reviewer-Complete-Brief.docx` (deleted), `CLAUDE.md`,
`.ai-context/templates/spec.template.md`, `.ai-context/templates/plan.template.md`,
`.ai-context/templates/tasks.template.md`, `.ai-context/templates/release.template.md`,
`.ai-context/project_context.md`, `.ai-context/status.md`, `.ai-context/prompt_history.md`
**Outcome:** Extracted the actual text of `docs/Requirement for SDD.docx` (the real requirement
source, previously unread) and confirmed it matches BRD-001 closely — same business context
(manual multi-team transfer process), same core requirement (self-service request with
status/pending-action visibility). Computed SHA-256 checksums for both real source documents
(`docs/INT SDD BluePrint - V1.0.pdf`, `docs/Requirement for SDD.docx`) and registered them in a
rewritten `source-docs/README.md`, replacing the four old Empty Floor PDFs and their proposal
extract. Deleted `SRS.md` outright — this project's constitution defines the chain as
BRD → Spec → Plan → Tasks with no SRS layer, so no replacement stub was needed. Cleared
`inventory/` to empty (`.gitkeep`) — a greenfield plugin has no existing code to inventory, unlike
the prior retro-spec back-fill. Rewrote `architecture.md` as an honest "nothing built yet"
placeholder documenting only the constitution's intended stack. Deleted the superseded
`int-standards.node.md` and rewrote `.agentignore` for PHP/WordPress paths. Rewrote all four
`.agent/workflows/*.md` files for PHPUnit/Brain Monkey/WP REST/`$wpdb`/`do_action`, replacing every
Vitest/Prisma/OpenAPI/Sourav-Tapash-Abhijit reference with this project's stack and `[Open]`
roster; deleted the stale `.docx` Gate 2 brief since a binary file cannot be safely regenerated by
an edit and its content now lives fully in `gate-2-reviewer.md`. Updated `CLAUDE.md`'s title and
rules-file link, and replaced its stale "blocked pending Phase 1" hard rule with the real current
state. Updated `spec.template.md`, `plan.template.md`, `tasks.template.md`, and
`release.template.md` to describe plugin/controller/service/repository structure and `dbDelta()`
migrations instead of the old Node/Prisma/OpenAPI conventions; left `adr.template.md` and
`hotfix-spec.template.md` untouched as already stack-agnostic. Updated `project_context.md` and
`status.md`'s Baseline artefacts table to stop flagging these files as stale, since they are now
current. No source code, specs, plans, or tasks were touched.
**Follow-up:** No previous-project content remains anywhere in `.ai-context/`, `.agent/`, or
`CLAUDE.md`, as far as this session found. BRD-001's two open ambiguities and the unnamed TL/PM
roster remain the standing follow-ups from the first Day 1 entry. Do not author
`specs/emp-internal-transfer.spec.md` until explicitly prompted to do so.

### 2026-08-30 — emp-internal-transfer.spec-authored

**Prompted by:** Developer (shamik.bhattacharya@intglobal.com)
**Instruction (summary):** Execute Day 2 of the SDD chain — author the initial Feature
Specification and testable Acceptance Criteria for `emp-internal-transfer` per
`docs/INT SDD BluePrint - V1.0.pdf`. Explicit requirements: Spec ID/Status/Linked-BRD header;
one-paragraph Intent; Context citing `architecture.md`/`constitution.md`; eight named acceptance
criteria (AC01–AC08) in strict Gherkin Given/When/Then covering initiation, tenure guard,
concurrency guard, the three approval transitions, status/timeline visibility, and authorization
isolation; explicit Out of Scope; Non-Functional Constraints sourced from the constitution. API
contract and spec-derived unit test tables to be left placeholder-ready for Day 3. No
implementation code, `.plan.md`, or `.tasks.md`.
**Artefacts touched:** `.ai-context/specs/emp-internal-transfer.spec.md` (new),
`.ai-context/status.md`, `.ai-context/prompt_history.md`
**Outcome:** Spec drafted `Draft v1.0` following the repository's 19-section canonical template
(`templates/spec.template.md`). §1 Intent is a single paragraph citing `BRD-001` directly. §2 Scope
lists the six journey stages in scope and names seven out-of-scope items — including two
(automatic SLA reminders, non-domestic transfers) that are out of scope specifically _because_
`BRD-001`'s own assumptions about them are unconfirmed, rather than the spec silently picking a
behaviour. §6 Functional requirements FR01–FR12 cover initiation through completion and
authorization isolation. §7 Non-functional requirements NFR01–NFR07 map directly to constitution
lines (p95 < 400 ms, zero PII in logs, capability + nonce checks, prepared SQL, 80% coverage
floor; the rate-limit decision itself is deferred to Day 3, flagged not silently missing). §12
Acceptance criteria are exactly the eight IDs specified (AC01–AC08), each strict Gherkin, each
individually identifiable. Resolved `BRD-001`'s "active transfer" phrase into a concrete, testable
definition (any stage other than `Completed`/`Rejected`) so AC03's concurrency guard has an
unambiguous boundary — this is a spec-author decision, not a new business rule, and is recorded as
such rather than left implicit. §11 Error scenarios ERR01–ERR08 are exhaustive against the AC/FR
set, including out-of-sequence-approval and the non-error `Rejected` terminal outcome. §8 API
contract and §13 Test scenarios are **explicitly placeholder** per instruction — routes/test IDs
named and mapped to FR/AC, schemas and concrete scenario text marked "TBD Day 3." §9 Data model is
conceptual (two tables, key fields named; no `dbDelta()` DDL yet). §16–17 Risks/Open questions
carry `BRD-001`'s two unconfirmed assumptions forward as `Q01`/`Q02`, add `Q03` (resubmission-
after-rejection cooldown — a new ambiguity found while writing AC03/FR04, not previously stated
anywhere) and `Q04` (no PM/TL named — this one **does** block Gate 1, unlike `Q01`–`Q03`). §19 Gate
1 checklist left unchecked; **Gate 1 decision recorded as Pending**, not Approved, because no
Gate 1 reviewer exists yet on the constitution's roster. `status.md` Active Specs row updated to
`Draft v1.0 (Spec Authored)`; Baseline artefacts and Programme status tables updated to reflect the
spec's existence. No source code, `.plan.md`, or `.tasks.md` written; no tests run — none exist
yet, consistent with §13 being placeholder.
**Follow-up:** A PM/TL still needs to be named on the constitution's roster before Gate 1 can
record a real decision (`Q04`). On Day 3: complete §8 API contract (request/response schemas, the
still-open rate-limit decision) and §13 spec-derived test scenarios, per
`.agent/workflows/generate-tests.md`. Do not begin `plans/emp-internal-transfer.plan.md` before
Gate 1 Approves this spec.

### 2026-08-31 — emp-internal-transfer.spec-finalized-for-gate1

**Prompted by:** Developer (shamik.bhattacharya@intglobal.com)
**Instruction (summary):** Execute Day 3 — finalize the spec by adding complete API contracts,
exception tables, and spec-derived unit test cases, and initialize the test_cases repository
file. Explicit requirements: Status → `In Peer Review (Gate 1)`; three named API contracts under
`## API Contract` (`API01` `POST /transfers` with 400/422 exceptions, `API02`
`GET /transfers/{id}` with 401/403/404 exceptions, `API03` `PATCH /transfers/{id}/approve`
handling all three sequential approval stages with 400/403/409 exceptions); a
`## Unit Test Cases (spec-derived)` table mapping `UT01`–`UT08` 1:1 to `AC01`–`AC08`; a new
`test_cases/emp-internal-transfer.test_cases.md` expanding into comprehensive QA scenarios (happy
path, boundary checks, tenure edge cases, RBAC rejections, validation failures). No `.plan.md`,
`.tasks.md`, or code.
**Artefacts touched:** `.ai-context/specs/emp-internal-transfer.spec.md`,
`.ai-context/test_cases/emp-internal-transfer.test_cases.md` (new), `.ai-context/status.md`,
`.ai-context/prompt_history.md`
**Outcome:** Spec Status → `In Peer Review (Gate 1)`. §8 API contract fully written: `API01`
(`POST /transfers`, full request/response JSON, exceptions 400/401/403/409/422/429), `API02`
(`GET /transfers/{id}`, full response JSON including the ordered timeline array, exceptions
401/403/404), `API03` (`PATCH /transfers/{id}/approve` — **consolidated from Day 2's three separate
per-stage placeholder routes into the single endpoint the instruction specified**; the request's
own `stage` field determines which of the three sequential actors is required, not the caller;
full request/response JSON including `downstream_dispatch_fired`, exceptions 400/401/403/404/409).
Made explicit, numbered rate-limit decisions (10/hr `API01`, 30/hr `API03`, none on read-only
`API02`), resolving `NFR06`'s prior "deferred to Day 3" state — flagged as the author's provisional
defaults pending TL/security sign-off via new `R04`/`Q05`, not silently asserted as final. §11
Error scenarios extended with `ERR09` (`forbidden_missing_capability`), `ERR10` (`rate_limited`),
`ERR11` (`transfer_not_found`) so the table stays exhaustive against the now-complete contract —
these three codes did not exist in the Day 2 draft and were only surfaced by writing the full API
detail. §13 renamed to "Unit Test Cases (spec-derived)" with exactly `UT01`–`UT08` mapped 1:1 to
`AC01`–`AC08`, each with a real scenario and expected result. Created
`test_cases/emp-internal-transfer.test_cases.md` — 53 scenarios across 10 categories (happy path 6,
boundary checks 6, tenure edge cases 5, concurrency guard 6, sequential-approval guard 9, RBAC/
authorization-isolation 6, validation failures 8, rate limiting 3, not-found 2, PII/logging 2),
every one mapped back to an AC/FR/ERR/API ID. While drafting the sequential-approval and RBAC
sections, precisely distinguished `403 forbidden_not_a_stakeholder` (wrong actor for a
still-decidable stage) from `409 invalid_stage_transition` (stage already fully processed — no
actor can decide) to avoid the two being conflated at implementation time; added `TC-RBAC-04` to
make explicit that the HR capability is portal-wide/unscoped, unlike the current-manager and
receiving-manager checks which are scoped to the specific person named on that request — this
distinction was implicit in the Day 2 Actors table but not tested for until this pass. `status.md`
Active Specs row, Baseline artefacts table, and Programme status table updated to match. No
implementation code, `.plan.md`, or `.tasks.md` written; no tests run — none exist yet, consistent
with test-first discipline (Red confirmation happens only once PHPUnit test files are written from
this document).
**Follow-up:** The spec is now fully ready for Gate 1 review in substance; the only remaining
blocker is procedural — a PM/TL still needs to be named on the constitution's roster (`Q04`) before
an actual Approved/Changes Requested decision can be recorded. `Q05` (rate-limit thresholds) is a
non-blocking confirmation item for whoever reviews. Do not begin
`plans/emp-internal-transfer.plan.md` until Gate 1 Approves.
