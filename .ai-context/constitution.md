# Project Constitution — One-Point Employee Portal

**Owner:** [Open] — Technical Lead not yet named · **Adopted:** 2026-08-29 · **Version:** v1.0 (Provisional)

Governs every feature this repository will ever build. Written once, amended rarely, and amended
only through the same review rigour as a spec. Each spec operates inside this document and never
restates it.

Standard in force: **INT Engineering Guidelines — Specification-Driven Delivery (SDD) v1.0**,
per `docs/INT SDD BluePrint - V1.0.pdf`. Rules below may be stricter than the standard; none may
weaken it.

> **Reset notice (2026-08-29).** This constitution supersedes all prior content in this file,
> which governed an unrelated Node.js/TypeScript project (Empty Floor + Circle Tap) cloned into
> this workspace. Nothing below carries any inherited assumption from that project. This is a
> **Day 1 Discovery** baseline: lines marked `[Provisional]` are working defaults pending sign-off;
> lines marked `[Open]` are not yet decided and block any spec that depends on them.

---

## Governance & Roles

| Role                       | Person       | Email                             | Responsibility                                                                   |
| -------------------------- | ------------ | --------------------------------- | -------------------------------------------------------------------------------- |
| Technical Lead / Architect | [Open] — TBD | —                                 | Owns this constitution; default Gate 2 reviewer; technical concurrence at Gate 1 |
| Spec Author                | Developer    | shamik.bhattacharya@intglobal.com | Default Spec Author for this feature's artefacts                                 |
| Project Manager            | [Open] — TBD | —                                 | Owns BRD entries and product-side sign-off; default Gate 1 reviewer              |

Roster is **[Open]** beyond the Spec Author. Until a TL and PM are named, Gate 1 and Gate 2
reviews cannot be formally recorded — this blocks any spec from reaching **Approved**, per the
gate rules below.

- **Gate 1 reviewer is never the author.** Default: Spec Author authors → PM reviews. If the PM
  authors a spec, Gate 1 must be someone else (TL or another senior engineer) — never
  self-approval.
- **Gate 1 requires recorded TL technical concurrence** whenever the spec touches Security
  Posture or Architectural Constraints below. The PM notes that concurrence on the Gate 1
  decision line; without it the spec is not Approved.
- Security or Architecture sign-off is required, not optional, whenever a plan touches anything
  under Security Posture or Architectural Constraints below.
- Gate 1 SLA: **[Open]** — no SLA has been agreed yet for this project.

### Named deviations from the standard

None recorded yet. This section stays empty until a deviation is actually taken — do not
pre-populate it from the prior project's deviations, which do not apply here.

### INT amendments to SDD v1.0 (mandatory)

These extend the published standard and are checked at Gate 1 like any other rule.

1. **Granular specification is mandatory.** The chain is **BRD → Spec → Plan → Tasks**. No
   feature is implemented by prompting directly against the codebase.
2. **Spec Review is the first quality gate.** Every spec is reviewed and approved by PM/TL/senior
   before development begins.
3. **Slugs and sub-identifiers are mandatory** for every spec, plan, and task, so prompts
   reference identity (`<slug>.T03`) rather than re-describing the feature.
4. **Status is maintained for every item** — BRD entries, specs, plans, and tasks — with daily
   progress updates in `status.md`.
5. **Releases and hotfixes are documented in `.ai-context/`** — releases in `releases/vX.Y.Z.md`,
   hotfix specs in `specs/hotfix-*.spec.md`.
6. **No implementation without an Approved spec** and an approved `tasks.md` derived from a
   reviewed plan. Work proceeds one task at a time, referenced by ID.
7. **Test-first is non-negotiable.** Tests exist, are reviewed, and are confirmed **failing**
   before implementation begins (Red → Green).

---

## Testing Discipline

- **Test-first is mandatory** for every custom REST endpoint and every hook (`do_action`) that
  triggers a state-changing or downstream-dispatch side effect. No exceptions for "simple"
  endpoints.
- Framework: **PHPUnit**, using **Brain Monkey** for unit-level WordPress function mocking and
  **`WP_UnitTestCase`** for integration tests against a bootstrapped WordPress instance.
- **Minimum 80% line coverage floor** on all custom endpoints and hooks introduced or changed by
  a task. Coverage is a floor, not a target to write to.
- A test that passes before its implementation exists is testing nothing. The Red confirmation is
  recorded on the task and verified at Gate 2.
- The PHPUnit suite and `phpcs` (WPCS ruleset, zero errors) must both pass before any task is
  considered done.

## Security Posture

- **Zero PII in logs, at any level, including debug.** PII here means: employee full name,
  employee ID, mobile number, personal or work email, department/location detail sufficient to
  identify an individual, and manager/reporting-line identity. No log sink — `error_log()`,
  `WP_DEBUG_LOG`, or any third-party integration log — ever receives these values in plaintext.
- **All REST routes are protected by WordPress authentication and capability checks.**
  `current_user_can()` (or a capability mapped via `map_meta_cap`) is mandatory on every route
  that reads or writes plugin data. A `permission_callback` of `__return_true` on a
  state-changing or PII-bearing route is a defect.
- **Nonce verification** is required on every authenticated write path, in addition to the
  capability check — a capability check is not a substitute for CSRF protection.
- **Rate-limiting is enabled** on public-facing or high-frequency endpoints. "No limit, because
  X" is an acceptable, explicit decision; silence is not.
- **Secrets are loaded via `wp-config.php` constants or environment variables** — never
  hardcoded, never logged, never committed.
- **Prepared SQL only.** Every `$wpdb` query touching external input goes through
  `$wpdb->prepare()`. No string-concatenated query with any external input, ever.
- Dependencies (Composer packages, must-use plugins) are vetted before entering the manifest.
  Prefer WordPress core APIs over a third-party library where core already covers the need.
- Secrets and PII never enter a spec, plan, task, prompt, or `prompt_history.md`.

## Architectural Constraints

- **Primary datastore is WordPress MySQL**, accessed via `$wpdb`. Custom tables (e.g.
  `wp_emp_transfers`) are used for transactional/workflow data rather than modelling it onto
  posts/postmeta. No new datastore without an ADR approved by the TL.
- **Asynchronous downstream dispatch** (to IT, Facilities, Payroll, or any other coordinator) is
  orchestrated via **WordPress Action Hooks** (`do_action`). No second queue, message broker, or
  scheduling mechanism without an ADR.
- **Layering is non-negotiable:** REST controllers extend `WP_REST_Controller` and contain
  request mapping only; business logic lives in service classes; `$wpdb` is accessed only from
  repository classes. See `.agent/rules/int-standards.wordpress.md`.
- All feature code is namespaced and lives under its own plugin directory
  (`/wp-content/plugins/<feature-slug>/`) — never loose in the parent theme.
- Schema changes go through a versioned `dbDelta()` migration keyed to a plugin `db_version`
  option. No hand-run `ALTER TABLE` against a shared environment.
- Cloud/hosting provider and environment topology: **[Open]** — not yet stated in the BRD or
  discovery inputs available so far.

## Non-Functional Baselines

Marked `[Provisional]` because no production measurement exists yet — they stay binding as design
targets and are refined once monitoring produces real numbers.

- **p95 API latency < 400 ms**, measured at the point closest to the client, for all custom REST
  endpoints under this plugin's namespace.
- **Availability target: 99.9%.**
- Recovery objectives (RPO/RTO): **[Open]** — not yet stated.
- Environments in scope: **[Open]** — not yet stated; assume at minimum Staging and Production
  until confirmed.

## Versioning Rules

- REST routes are **namespace-versioned** — `/wp-json/one-point/v1/...`. A breaking change to a
  released route requires a new version segment and an ADR documenting the break. Silent
  breaking changes are prohibited.
- Releases are documented in `releases/vX.Y.Z.md`, with each constituent spec's status flipped to
  `Released (vX.Y.Z)`.

---

## Repository & Branching

- One feature branch per spec: `feature/<feature-slug>`, `fix/<issue-slug>`,
  `hotfix/<incident-slug>` — always traceable to the spec file of the same slug.
- Merge target and branching model: **[Open]** — not yet decided for this repository.
- No agent-signature suffixes in commit messages. `Implements <slug>.T03` is traceability and is
  encouraged.
- If a PR cannot be reviewed against its spec's acceptance criteria in one sitting, the spec was
  scoped too large — split it.
- `.agent/` and `.ai-context/` are **internal-repo only** and are not shipped to any
  client-facing repository.
- Trivial-change tier — config bumps, dependency version updates, formatting, and docs-only edits
  with no behaviour change — skips spec/plan/tasks but still requires a `status.md` note and a
  reviewed PR. Anything that changes behaviour is not trivial.
