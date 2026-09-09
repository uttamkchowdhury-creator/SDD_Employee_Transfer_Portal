# Project Status Board

_Last updated: 2026-08-31 — updated by: Developer_

Single source of truth for **what is happening right now**. Updated the same day by whoever last
touched an artefact.

> **Reset notice (2026-08-29).** This board previously tracked a 96-spec retro-spec back-fill
> programme for an unrelated Node.js project (Empty Floor + Circle Tap). That entire programme,
> its wave structure, and its risk register have been retired along with the rest of that
> project's context — none of it applies here. This board now tracks the **One-Point Employee
> Portal**, starting with the Employee Internal Transfer Journey.

---

## Programme status

| Item                            | State                                                                                                                                              |
| ------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| SDD v1.0 adoption               | **Reset 2026-08-29** — `.ai-context/` and `.agent/` repopulated for the new project                                                                |
| Constitution                    | v1.0 (Provisional) — governance roster largely `[Open]`; TL and PM not yet named                                                                   |
| WordPress coding standards      | Established 2026-08-29 — `.agent/rules/int-standards.wordpress.md`                                                                                 |
| BRD-001                         | Authored 2026-08-29 — Employee Internal Transfer Digital Journey; Status `Open` pending PM/business sign-off                                       |
| `emp-internal-transfer.spec.md` | `In Peer Review (Gate 1)` since 2026-08-31 — FR01–FR12, NFR01–NFR07, AC01–AC08, ERR01–ERR11, API01–API03 all complete                              |
| New feature work                | **Blocked** — spec is `In Peer Review (Gate 1)`, not yet Approved (no PM/TL named — `Q04`); no plan/tasks/implementation may start before Approval |

## Active specs

| Spec ID                 | Title                              | Status                      | Owner     | Last Updated | Notes                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| ----------------------- | ---------------------------------- | --------------------------- | --------- | ------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `emp-internal-transfer` | Employee Internal Transfer Journey | **In Peer Review (Gate 1)** | Developer | 2026-08-31   | Spec finalized — §8 API contract (`API01`–`API03`, full request/response/exception detail), §11 Error scenarios extended to `ERR09`–`ERR11`, §13 Unit Test Cases (`UT01`–`UT08`, 1:1 with `AC01`–`AC08`), and `test_cases/emp-internal-transfer.test_cases.md` (53 QA-expanded scenarios) all complete. **Still blocked from an actual Gate 1 decision:** no PM/TL named yet (`Q04`). Next: name a Gate 1 reviewer; do not start the Plan until Gate 1 Approves. |

## Baseline artefacts

| Artefact                                         | Status                                                                                                                         | Owner     | Note                                                                                                                    |
| ------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------ | --------- | ----------------------------------------------------------------------------------------------------------------------- |
| `constitution.md`                                | Reset — v1.0 (Provisional)                                                                                                     | Developer | TL/PM roster `[Open]`; several NFR/architecture lines `[Open]` pending business input                                   |
| `project_context.md`                             | Reset — Current as of 2026-08-29                                                                                               | Developer | —                                                                                                                       |
| `architecture.md`                                | Reset — placeholder as of 2026-08-29                                                                                           | Developer | Documents intended stack only; nothing built yet                                                                        |
| `BRD.md`                                         | Seeded — BRD-001 only                                                                                                          | Developer | Open items: SLA/reminder assumption, domestic-only assumption, no PM/TL named                                           |
| `source-docs/README.md`                          | Reset — registers the two real project source documents (`docs/INT SDD BluePrint - V1.0.pdf`, `docs/Requirement for SDD.docx`) | Developer | Both checked into the repo directly — no external shared storage needed                                                 |
| `specs/emp-internal-transfer.spec.md`            | `In Peer Review (Gate 1)`                                                                                                      | Developer | Finalized 2026-08-31; see Active specs row above                                                                        |
| `test_cases/emp-internal-transfer.test_cases.md` | Current                                                                                                                        | Developer | 53 QA-expanded scenarios across 10 categories, authored 2026-08-31                                                      |
| `plans/`, `tasks/`, `decisions/`, `inventory/`   | Empty (`.gitkeep` only)                                                                                                        | —         | Previous project's artefacts removed 2026-08-29; `inventory/` stays empty — greenfield plugin, nothing to inventory yet |
| `releases/`                                      | Empty                                                                                                                          | —         | First entry at first post-SDD release                                                                                   |
| `.agent/rules/int-standards.wordpress.md`        | Established 2026-08-29                                                                                                         | Developer | Replaces the removed `int-standards.node.md`                                                                            |
| `.agent/workflows/*.md`                          | Rewritten 2026-08-29 for the WordPress/PHPUnit stack                                                                           | Developer | `Gate-2-Reviewer-Complete-Brief.docx` removed — content now lives in `gate-2-reviewer.md`                               |

## Engineering risks

None recorded yet — no code exists for this feature, and no spec has been authored to surface
as-built findings. This section will populate once spec authoring begins.

## Daily Execution Log

### 2026-08-31

- **Day 3 — Spec finalized for Gate 1: API contracts, exception tables, and spec-derived unit
  test cases.** Updated `.ai-context/specs/emp-internal-transfer.spec.md`, Status →
  `In Peer Review (Gate 1)`. Changes:
  - **§8 API contract** fully specified — three routes, consolidated from Day 2's five-route
    placeholder into exactly three (`BRD-001`'s namespace, `/wp-json/one-point/v1/transfers`):
    `API01` `POST /transfers` (initiate — full request/response JSON, 400/401/403/409/422/429
    exception table), `API02` `GET /transfers/{id}` (status + timeline — full response JSON,
    401/403/404 exception table), `API03` `PATCH /transfers/{id}/approve` (**one** endpoint
    servicing all three sequential approval stages — the request's own `stage` field determines
    which actor is required, not the caller; full request/response JSON, 400/401/403/404/409
    exception table). Explicit rate-limit decisions made (10/hr on `API01`, 30/hr on `API03`, none
    on the read-only `API02`) — resolving `NFR06`'s prior deferral, flagged as provisional pending
    TL/security sign-off (new `R04`/`Q05`).
  - **§11 Error scenarios** extended with `ERR09` (`forbidden_missing_capability`, distinct from
    `ERR06`'s "right capability, wrong person for this request"), `ERR10` (`rate_limited`), and
    `ERR11` (`transfer_not_found`) — all newly introduced by the finalized API contract, added so
    the exception table stays exhaustive per the Gate 1 checklist rather than drifting out of sync
    with §8.
  - **§13**, renamed from "Test scenarios" to **"Unit Test Cases (spec-derived)"** per instruction
    — exactly `UT01`–`UT08`, 1:1 with `AC01`–`AC08`, each with a concrete scenario and expected
    result (no longer "TBD Day 3").
  - **§16/§17** — added `R04` (rate-limit numbers are the author's proposed defaults, not
    confirmed) and `Q05` (same, phrased as the confirmation needed); `§18` Traceability's Tests row
    now points to both §13 and the new test_cases file instead of "not yet authored."
  - **Author/Reviewer note** updated: the spec is now complete and submitted for peer review, but
    a real Gate 1 decision still cannot be recorded until a PM is named (`Q04`, `R02` — unchanged
    from Day 2).
  - Created **`.ai-context/test_cases/emp-internal-transfer.test_cases.md`** — 53 QA/developer-
    expanded scenarios across 10 categories (happy path, boundary checks, tenure edge cases,
    concurrency guard, sequential-approval guard, RBAC/authorization-isolation rejections,
    validation failures, rate limiting, not-found, PII/logging verification), each mapped back to
    an AC/FR/ERR/API ID. Notably resolved a potential ambiguity while writing the RBAC/sequential
    sections: distinguished `403 forbidden_not_a_stakeholder` (wrong actor for a still-decidable
    stage) from `409 invalid_stage_transition` (no actor can decide — stage already fully
    processed) precisely, and added `TC-RBAC-04` to make explicit that the HR capability is
    portal-wide/unscoped, unlike the current-manager and receiving-manager checks which are scoped
    to the specific person named on that request.
    No implementation code, `.plan.md`, or `.tasks.md` were written, per instruction.

**Next:** Name a Gate 1 reviewer (PM) so this spec can receive an actual Approved/Changes
Requested decision — it is fully ready for review as of today. Do not begin
`plans/emp-internal-transfer.plan.md` before that happens.

### 2026-08-30

- **Day 2 — Feature Specification and Acceptance Criteria authored for `emp-internal-transfer`.**
  Created `.ai-context/specs/emp-internal-transfer.spec.md`, Status `Draft v1.0`, following
  `templates/spec.template.md`'s 19-section structure. Content:
  - **Intent** (§1): single paragraph defining what changes, for whom, under what conditions,
    directly citing `BRD-001`.
  - **Scope** (§2): in-scope list matching the six journey stages; out-of-scope list names
    downstream coordinators' own provisioning logic, compensation renegotiation,
    cross-border tax/legal (per `BRD-001`), SLA reminders (pending `BRD-001` confirmation),
    non-domestic transfers (pending `BRD-001` confirmation), employee-initiated cancellation, and
    admin-initiated bulk transfers — none of the last four were silently assumed.
  - **Functional requirements** FR01–FR12 covering initiation, both guards, all three approval
    transitions, downstream dispatch, completion, status/timeline visibility, and authorization
    isolation.
  - **Non-functional requirements** NFR01–NFR07 sourced directly from `constitution.md` (p95 < 400
    ms, zero PII in logs, capability + nonce checks, prepared SQL, rate-limit decision deferred to
    Day 3, 80% coverage floor).
  - **Acceptance criteria** AC01–AC08, all in strict Gherkin (Given/When/Then), exactly the eight
    IDs specified: successful initiation (AC01), tenure guard (AC02), concurrency/single-active-
    transfer guard (AC03), the three approval transitions (AC04–AC06), status/timeline visibility
    (AC07), and authorization isolation (AC08). Resolved `BRD-001`'s "active transfer" phrase into
    an explicit, testable definition (any stage other than `Completed`/`Rejected`) so AC03 has a
    concrete boundary rather than an ambiguous one.
  - **Error scenarios** ERR01–ERR08, exhaustive against the AC/FR set including out-of-sequence
    approval attempts and the non-error `Rejected` terminal outcome.
  - **API contract** (§8) and **Test scenarios** (§13) deliberately left **placeholder** per
    instruction — routes and test IDs are named and mapped to FR/AC, but request/response schemas,
    rate-limit decisions, and concrete test bodies are marked TBD for Day 3.
  - **Data model** (§9) kept conceptual — two tables (`wp_emp_transfers`, `wp_emp_transfer_events`)
    and their key fields named; the actual `dbDelta()` DDL is a Day 3/Plan artefact, not authored
    here.
  - **Risks/Open questions** (§16–17): carried `BRD-001`'s two unconfirmed assumptions forward as
    `Q01`/`Q02` (both resolved for now by keeping the related behaviour out of scope rather than
    guessing); added `Q03` (resubmission-after-rejection cooldown, not stated in `BRD-001`) and
    `Q04` (no TL/PM named — this **does** block a real Gate 1 decision, unlike `Q01`–`Q03`).
  - Gate 1 review checklist (§19) left unchecked; **Gate 1 decision: Pending** — cannot be
    Approved until a PM is named per the constitution's roster (`Q04`, `R02`).
    No implementation code, `.plan.md`, or `.tasks.md` were written, per instruction.

**Next:** Name a Gate 1 reviewer (PM) on the constitution's roster so this spec can actually be
reviewed. On Day 3: complete §8 API contract (request/response schemas, rate-limit decisions) and
§13 spec-derived test scenarios per `.agent/workflows/generate-tests.md`. Do not begin
`plans/emp-internal-transfer.plan.md` before Gate 1 Approves.

### 2026-08-29 (cont.)

- **Full old-project purge, round 2.** The first Day 1 pass only cleared the five artefact
  directories the original instruction listed; a follow-up request ("remove all everything of old
  project replace with my project") caught the remaining Empty Floor + Circle Tap residue this
  workspace still carried:
  - Deleted `.ai-context/SRS.md` (no SRS layer in this project's methodology — BRD is the sole
    requirement-baseline artefact).
  - Deleted the four old requirement PDFs and `proposal-extract.md` from `source-docs/`; rewrote
    `source-docs/README.md` as a registry for this project's two real source documents
    (`docs/INT SDD BluePrint - V1.0.pdf`, `docs/Requirement for SDD.docx`), with SHA-256 checksums
    computed directly. Confirmed via the extracted requirement text that BRD-001 already matches
    the assessment brief's business context and requirement almost exactly.
  - Cleared `.ai-context/inventory/` (4 old inventories) to `.gitkeep` — this is a greenfield
    plugin with no existing code to inventory, unlike a retro-spec back-fill.
  - Rewrote `architecture.md` as an honest placeholder (intended stack per the constitution;
    nothing built yet) rather than leaving Empty Floor's Express/Prisma architecture in place.
  - Deleted `.agent/rules/int-standards.node.md` (superseded by `int-standards.wordpress.md`,
    authored in the first pass).
  - Rewrote all four `.agent/workflows/*.md` files (`code-review`, `generate-plan`,
    `generate-tests`, `gate-2-reviewer`) for PHPUnit/Brain Monkey/WP REST/`$wpdb` instead of
    Vitest/Prisma/OpenAPI; removed roster names no longer accurate for this project (Sourav/Tapash/
    Abhijit → `[Open]` per `constitution.md`). Deleted the stale
    `Gate-2-Reviewer-Complete-Brief.docx` — its content now lives entirely in the rewritten
    `gate-2-reviewer.md` (a binary `.docx` cannot be safely regenerated by an edit).
  - Rewrote `.agent/rules/.agentignore` for the PHP/WordPress stack (`vendor/`, `wp-admin/`,
    `wp-includes/`, `wp-config.php` in place of `node_modules/`, Prisma-generated client, `.env`).
  - Updated `CLAUDE.md` — title changed from "Empty Floor + Circle Tap API" to "One-Point Employee
    Portal"; rules-file link now points to `int-standards.wordpress.md`; the stale "blocked pending
    Phase 1 retro-spec back-fill" hard rule replaced with the actual current state (Discovery, no
    spec yet for `emp-internal-transfer`).
  - Rewrote `templates/spec.template.md`, `templates/plan.template.md` (data model/architecture
    sections now describe plugin/controller/service/repository structure and `dbDelta()`, not
    Prisma), `templates/tasks.template.md` (OpenAPI-update example task replaced), and
    `templates/release.template.md` (migrations/contract-check lines de-Prisma'd, `Dev`-branch
    assumption removed). `adr.template.md` and `hotfix-spec.template.md` were already
    stack-agnostic and needed no change.
  - `project_context.md` and this board's Baseline artefacts table updated to reflect the new,
    clean state instead of flagging these files as stale.
    No source code, specs, plans, or tasks were touched — this was entirely `.ai-context/`/`.agent/`
    and root-level documentation cleanup.

**Next:** Nothing further from the previous project remains referenced anywhere in
`.ai-context/`, `.agent/`, or `CLAUDE.md`. Resume the Day 1 follow-ups below.

### 2026-08-29

- **Day 1 Discovery & Requirement Analysis completed for `emp-internal-transfer`.** Full reset of
  `.ai-context/` and `.agent/rules/` from the prior, unrelated Node.js/TypeScript project (Empty
  Floor + Circle Tap) to the new WordPress-based **One-Point Employee Portal**. Actions taken:
  - Cleared all prior-project artefacts from `specs/`, `plans/`, `tasks/`, `test_cases/`, and
    `decisions/` (11 specs, 1 plan, 1 tasks file, 2 test-case files removed); `.gitkeep`
    placeholders restored in each directory.
  - Authored `.agent/rules/int-standards.wordpress.md` — WPCS + PHP 8.x strict typing,
    `WP_REST_Controller` convention, PHPUnit/Brain Monkey test discipline, capability/nonce/
    sanitization/escaping/prepared-SQL security rules, zero-PII-in-logs rule.
  - Rewrote `constitution.md` for the new stack: PHPUnit test-first with an 80% coverage floor;
    Security Posture (zero PII in logs, WP auth + capability checks on all REST routes,
    rate-limiting, `wp-config.php`/env secrets); Architectural Constraints (WordPress MySQL via
    `$wpdb` as system of record, `do_action`-based async downstream dispatch); Non-Functional
    Baselines (p95 < 400 ms, 99.9% availability). Governance roster (TL, PM) left `[Open]` — not
    yet named for this project.
  - Rewrote `project_context.md` — defines the One-Point Employee Portal objective and its role
    as the centralized self-service gateway for internal transfers; flags `architecture.md`,
    `SRS.md`, `inventory/`, and `source-docs/` as stale prior-project content out of scope for
    this pass.
  - Authored **BRD-001: Employee Internal Transfer Digital Journey** in `BRD.md` — business
    problem, objective, primary actors, six-stage digital journey, Business vs Technical
    decisions split, two open ambiguities (5-day SLA with reminders; domestic-only v1 scope), and
    explicit out-of-scope items (cross-border tax compliance, inter-entity legal contract
    changes). Status recorded as `Open` pending business confirmation of both ambiguities and a
    named PM/TL.
  - This board reset to drop the prior project's 96-spec retro-back-fill programme entirely and
    reflect only this feature.
  - No code, specs (`.spec.md`), or technical plans (`.plan.md`) were written — Discovery and
    repository context setup only, per explicit instruction.

  **Next:** Get BRD-001's two open ambiguities confirmed with a business owner, and get a TL and
  PM named on the constitution's roster. Author the feature spec from BRD-001 only when prompted
  — do not proceed to spec/plan/tasks in this same pass.
