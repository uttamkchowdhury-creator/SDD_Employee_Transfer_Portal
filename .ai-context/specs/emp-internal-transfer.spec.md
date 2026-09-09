# Spec: Employee Internal Transfer Journey

> Follows [.ai-context/templates/spec.template.md](../templates/spec.template.md). Retro-spec
> guidance in that template's header does not apply — this is a greenfield feature, not
> documentation of already-shipped behaviour.

## Spec ID
`emp-internal-transfer`

## Status
In Peer Review (Gate 1)
<!-- Draft → In Peer Review (Gate 1) → Changes Requested ⟲ → Approved → Plan Drafted →
     Plan Reviewed → Tasks Generated → In Development → In QA → Ready for Release →
     Released (vX.Y.Z) → [Deprecated / Superseded]. Nothing skips a state. -->

## Author / Reviewer
**Author:** Developer (shamik.bhattacharya@intglobal.com) ·
**Gate 1 reviewer:** `[Open]` — no Project Manager named yet on the constitution's roster
(see [constitution.md](../constitution.md) → Governance & Roles). The spec is now complete and
submitted for peer review (API contract, exception tables, and spec-derived test cases all
finalized as of Day 3), but a real Gate 1 **decision** cannot be recorded — Approved or Changes
Requested — until a reviewer is actually named (`emp-internal-transfer.Q04`,
`emp-internal-transfer.R02`).

## Linked BRD
[`.ai-context/BRD.md#BRD-001`](../BRD.md) — Employee Internal Transfer Digital Journey

---

## 1. Business objective (Intent)

**Intent:** An employee with at least six months of tenure in their current role who has no other
transfer request currently in flight may initiate, through the One-Point Employee Portal, a request
to move to a different department, location, and/or role, effective no sooner than 30 days out;
that request is then routed through a fixed sequence of three approvals — the employee's current
reporting manager, an HR Business Partner validating policy eligibility, and the receiving
department's manager — after which the portal asynchronously notifies IT, Facilities, and Payroll
to provision the change, and the requester and every stakeholder named on the request can see its
current stage and full history at any time, while anyone not named on that specific request is
denied both visibility and the ability to act on it. This replaces the ad hoc email/spreadsheet
process described in `BRD-001`'s Business Problem with one auditable, policy-enforced, single-pane
workflow.

## 2. Scope / Out of scope

### In scope

- Request initiation by the employee (target department, target location, target role, effective
  date, optional reason) — see `emp-internal-transfer.AC01`.
- The 6-month tenure eligibility guard and the single-active-transfer concurrency guard —
  `emp-internal-transfer.AC02`, `AC03`.
- The three sequential approval transitions (Current Manager → HR → Receiving Manager) —
  `AC04`–`AC06`.
- Dispatching an asynchronous downstream-provisioning notification via WordPress Action Hooks
  (`do_action`) once the Receiving Manager accepts — this spec owns firing the hook, not what IT,
  Facilities, or Payroll do in response to it (see Out of scope below).
- Status inquiry and audit-timeline visibility for the requester and every named stakeholder —
  `AC07`.
- Authorization isolation — denying view/act access to anyone not named on a given request —
  `AC08`.

### Out of scope

- **IT, Facilities, and Payroll's own provisioning logic** (account creation, badge/desk
  reassignment, compensation or reporting-line system updates). This spec fires the `do_action`
  hook and documents its payload contract; what a listener does with it belongs to that
  coordinator's own feature slug, not this one.
- **Compensation renegotiation.** A transfer may imply a role or grade change, but negotiating or
  recording a new compensation figure is a separate, unrelated workflow — this spec carries no
  salary or grade field.
- **Cross-border tax compliance and inter-entity legal contract changes** — explicitly excluded by
  `BRD-001`.
- **Automatic SLA reminder / escalation notifications.** `BRD-001`'s 5-day-SLA-with-reminders
  ambiguity is unconfirmed by a business owner (see `BRD-001` Open Ambiguities and
  `emp-internal-transfer.Q01` below). No reminder, escalation, or SLA-timeout behaviour is
  specified here; each approval stage waits indefinitely for its actor. Reminders are a
  candidate addition for a later spec revision once confirmed, not a silent gap in this one.
- **Non-domestic (cross-location, cross-country) transfers.** `BRD-001` assumes domestic-only for
  v1 but that assumption is itself unconfirmed (`emp-internal-transfer.Q02`). This spec does not
  add a country/legal-entity field or any cross-border guard — it is silent on the distinction
  entirely, which is only safe because v1's assumed scope is domestic. If that assumption is
  wrong, this spec needs a revision, not a workaround at implementation time.
- **Employee-initiated cancellation or withdrawal** of a submitted request. `BRD-001` does not
  describe this capability; it is not modelled as a stage or an acceptance criterion here.
- **Bulk/administrative transfer initiation** on behalf of an employee (e.g., an HR-initiated
  reorganisation). This spec covers only self-service initiation by the employee themselves.

## 3. Dependencies

| Kind | Reference | Why |
|---|---|---|
| Spec | none | First feature on this portal — no prior spec to depend on |
| BRD | `BRD-001` | Sole requirement source for this feature |
| ADR | none | No architectural decision has been raised yet |
| Runtime | WordPress Action Hooks (`do_action`) | Downstream dispatch to IT/Facilities/Payroll listeners; if no listener is registered for a hook, the dispatch is a silent no-op — tracked as `emp-internal-transfer.R01` |
| Code | `wp-content/plugins/emp-internal-transfer/` (does not exist yet) | This spec's entire owned surface |

## 4. Domain context

- **Plugin:** `wp-content/plugins/emp-internal-transfer/`
- **Architecture:** [architecture.md](../architecture.md) §1 (intended stack — nothing is built
  yet; this spec is what will eventually populate §2–§4 of that document once a plan exists)
- **Constitution:** [constitution.md](../constitution.md) — Testing Discipline, Security Posture,
  Architectural Constraints, and Non-Functional Baselines sections all apply directly (§7 below
  restates the applicable lines; this spec introduces no exception to any of them)
- **Portal capability context:** this is the **first** feature built on the One-Point Employee
  Portal (see [project_context.md](../project_context.md)) — there is no existing plugin, capability
  scheme, or custom table to integrate with. This spec establishes the first custom table
  (`wp_emp_transfers`) and the first REST namespace usage (`one-point/v1`) for the portal.

## 5. Actors / Roles

| Actor | Capability posture (proposed — registered at the Plan stage) | What they do here |
|---|---|---|
| Requester (Employee) | `emp_transfer:submit`, `emp_transfer:view_own` | Initiates a request; views its status/timeline |
| Current Reporting Manager | `emp_transfer:approve_manager` (scoped to requests where they are the named current manager) | Approves or rejects release at Stage 2 |
| HR Business Partner | `emp_transfer:validate_hr` | Approves or rejects eligibility at Stage 3 |
| Receiving Department Manager | `emp_transfer:accept_receiving` (scoped to requests where they are the named receiving manager) | Accepts or declines the incoming employee at Stage 4 |
| Downstream Coordinators (IT, Facilities, Payroll) | none (not portal users for this spec) | Consume the `do_action` dispatch fired at Stage 5; not authenticated portal actors — out of scope per §2 |
| Any other authenticated user | none of the above | Must be denied view/act access — `emp-internal-transfer.AC08` |
| Unauthenticated caller | none | Denied on every route — `emp-internal-transfer.ERR07` |

## 6. Functional requirements

| ID | Requirement |
|---|---|
| `emp-internal-transfer.FR01` | The system shall allow an authenticated employee to submit a new Internal Transfer Request specifying target department, target location, target role, an effective date, and an optional reason. |
| `emp-internal-transfer.FR02` | The system shall reject a request whose effective date is less than 30 days from the submission date. |
| `emp-internal-transfer.FR03` | The system shall reject a request from an employee with less than 6 months of tenure in their current role/department. |
| `emp-internal-transfer.FR04` | The system shall reject a new request if the requesting employee already has a request in an **active** stage (any stage other than `Completed` or `Rejected` — matching `BRD-001`'s "non-completed, non-rejected" definition). |
| `emp-internal-transfer.FR05` | The system shall route a `Submitted` request to the employee's Current Reporting Manager, and no other stage may act on it before this approval is recorded. |
| `emp-internal-transfer.FR06` | The system shall route a `Manager-Approved` request to HR for eligibility validation, strictly after Stage 2, never before. |
| `emp-internal-transfer.FR07` | The system shall route an `HR-Validated` request to the named Receiving Department Manager, strictly after Stage 3, never before. |
| `emp-internal-transfer.FR08` | The system shall allow a rejection at the Current Manager, HR, or Receiving Manager stage to transition the request to a terminal `Rejected` state, recording the rejecting actor, stage, and an optional reason. |
| `emp-internal-transfer.FR09` | The system shall dispatch an asynchronous downstream-provisioning notification via `do_action()` to IT, Facilities, and Payroll listeners immediately once the Receiving Manager accepts (Stage 4 → Stage 5). |
| `emp-internal-transfer.FR10` | The system shall provide a mechanism for a request to be marked `Completed` once downstream provisioning is acknowledged. *(The acknowledgement mechanism itself — e.g. a callback hook vs. a manual admin action — is a technical design decision deferred to the Plan stage; this FR fixes only the observable behaviour.)* |
| `emp-internal-transfer.FR11` | The system shall let the requester and every actor named at any stage of a specific request view that request's current stage, the identity of the actor it is currently pending with, and its full ordered stage-by-stage history in real time. |
| `emp-internal-transfer.FR12` | The system shall deny both view and act access to any user who is neither the requester nor named as an actor at any stage of that specific request. |

## 7. Non-functional requirements

| ID | Requirement | Source |
|---|---|---|
| `emp-internal-transfer.NFR01` | p95 latency < 400 ms on every route this spec owns | constitution — Non-Functional Baselines |
| `emp-internal-transfer.NFR02` | Zero PII in logs at any level — employee name, ID, mobile/email, department/location detail, and manager identity never reach `error_log()`/`WP_DEBUG_LOG` in plaintext | constitution — Security Posture |
| `emp-internal-transfer.NFR03` | Every route requires a `current_user_can()` capability check; no route uses `__return_true` | constitution — Security Posture |
| `emp-internal-transfer.NFR04` | Every authenticated write route requires nonce verification in addition to the capability check | constitution — Security Posture |
| `emp-internal-transfer.NFR05` | Every `$wpdb` query touching external input uses `$wpdb->prepare()` | constitution — Security Posture |
| `emp-internal-transfer.NFR06` | Explicit rate-limit decision required on every route this spec owns — see §8 per route; numeric values are the spec author's proposed defaults pending TL/security sign-off (`emp-internal-transfer.Q05`) | constitution — Security Posture |
| `emp-internal-transfer.NFR07` | 80% line coverage floor on changed files, PHPUnit + Brain Monkey / `WP_UnitTestCase` | constitution — Testing Discipline |

## 8. API contract

Three REST routes fully specify this feature's HTTP surface, all under the WP REST namespace
`one-point/v1`. All write routes require the standard WordPress REST nonce (`X-WP-Nonce`) in
addition to the capability/relationship check named per route — a missing or invalid nonce is
folded into `401`/`unauthorized`, not a separate error code, per WordPress's own
`rest_cookie_check_errors` behaviour.

| ID | Route | Satisfies |
|---|---|---|
| `API01` | `POST /wp-json/one-point/v1/transfers` | `FR01`–`FR04`, `AC01`–`AC03` |
| `API02` | `GET /wp-json/one-point/v1/transfers/{id}` | `FR11`, `AC07`; enforces `FR12`/`AC08` |
| `API03` | `PATCH /wp-json/one-point/v1/transfers/{id}/approve` | `FR05`–`FR09`, `AC04`–`AC06`; enforces `ERR05`, `ERR06`, `ERR08` |

### `emp-internal-transfer.API01` — `POST /wp-json/one-point/v1/transfers`

**Purpose:** Initiate a new Internal Transfer Request.
**Auth:** `current_user_can('emp_transfer:submit')` — any authenticated employee.
**Nonce:** required.
**Rate limit:** 10 requests / rolling hour per user. Deliberately low: a legitimate employee
submits at most one request at a time, and `AC03`'s concurrency guard already blocks a second
submission while one is active — this ceiling exists to blunt scripted retry/abuse, not to
constrain normal use.

**Request:**
```json
{
  "target_department": "string, required — department slug or ID",
  "target_location": "string, required — location slug or ID",
  "target_role": "string, required — target job role/position title or ID",
  "effective_date": "string, required — ISO 8601 date (YYYY-MM-DD), must be >= 30 days from today",
  "reason": "string, optional — free text, max 500 characters"
}
```

**Success (`201 Created`):**
```json
{
  "id": 123,
  "requester_user_id": 45,
  "target_department": "sales",
  "target_location": "mumbai",
  "target_role": "senior-account-executive",
  "effective_date": "2026-10-15",
  "reason": "Relocating for family reasons",
  "stage": "submitted",
  "pending_with_user_id": 78,
  "created_at": "2026-08-31T09:12:00+00:00"
}
```

**Exceptions:**

| HTTP | `error.code` | Condition | Ref |
|---|---|---|---|
| 400 | `validation_error` | A required field is missing, wrong type, or fails a basic format check (e.g. `effective_date` is not a valid ISO 8601 date) | `ERR04` |
| 401 | `unauthorized` | No authenticated session, or missing/invalid nonce | `ERR07` |
| 403 | `forbidden_missing_capability` | Authenticated, but the user does not hold `emp_transfer:submit` | `ERR09` |
| 409 | `active_transfer_exists` | Requester already has a transfer request in an active stage | `ERR03` / `AC03` |
| 422 | `effective_date_too_soon` | `effective_date` is less than 30 days from the submission date | `ERR01` / `AC01` |
| 422 | `ineligible_tenure` | Requester's tenure in their current role/department is less than 6 months | `ERR02` / `AC02` |
| 429 | `rate_limited` | The rate limit above was exceeded | `ERR10` |

### `emp-internal-transfer.API02` — `GET /wp-json/one-point/v1/transfers/{id}`

**Purpose:** Retrieve a single transfer request's current state and full audit timeline.
**Auth:** the caller must be the request's requester **or** named as an actor (current manager,
HR approver, or receiving manager) at any stage of *this specific request* — holding a capability
alone is not sufficient (see §10 Permissions / Security).
**Nonce:** not required — read-only `GET`, per standard WP REST convention; the relationship check
above still applies in full regardless.
**Rate limit:** none. Read-only, scoped to requests the caller is already entitled to see; no abuse
surface distinct from a user normally polling their own request's status.

**Success (`200 OK`):**
```json
{
  "id": 123,
  "requester_user_id": 45,
  "target_department": "sales",
  "target_location": "mumbai",
  "target_role": "senior-account-executive",
  "effective_date": "2026-10-15",
  "reason": "Relocating for family reasons",
  "stage": "manager_approved",
  "pending_with_user_id": 91,
  "current_manager_user_id": 78,
  "hr_approver_user_id": null,
  "receiving_manager_user_id": null,
  "rejected_by_user_id": null,
  "rejection_reason": null,
  "timeline": [
    { "stage": "submitted", "actor_user_id": 45, "decision": "submitted", "reason": null, "occurred_at": "2026-08-31T09:12:00+00:00" },
    { "stage": "manager_approved", "actor_user_id": 78, "decision": "approved", "reason": null, "occurred_at": "2026-09-02T11:05:00+00:00" }
  ],
  "created_at": "2026-08-31T09:12:00+00:00",
  "updated_at": "2026-09-02T11:05:00+00:00"
}
```

**Exceptions:**

| HTTP | `error.code` | Condition | Ref |
|---|---|---|---|
| 401 | `unauthorized` | No authenticated session | `ERR07` |
| 403 | `forbidden_not_a_stakeholder` | Authenticated, but is neither the requester nor a named actor on this specific request | `ERR06` / `AC08` |
| 404 | `transfer_not_found` | No transfer request exists with the given `{id}` | `ERR11` |

### `emp-internal-transfer.API03` — `PATCH /wp-json/one-point/v1/transfers/{id}/approve`

**Purpose:** Record a decision (approve or reject) at whichever of the three sequential approval
stages the request currently sits at — Current Manager, HR, or Receiving Manager. **One endpoint
services all three stages**; the system determines which stage applies from the request's own
`stage` field, never from the caller, so there is no separate route per stage to keep in sync.
**Auth:** the caller must be the specific actor required for the request's *current* stage — a user
holding `emp_transfer:approve_manager` **and** named as this request's current manager when
`stage = submitted`; a user holding `emp_transfer:validate_hr` (portal-wide, not scoped to a named
person) when `stage = manager_approved`; or a user holding `emp_transfer:accept_receiving` **and**
named as this request's receiving manager when `stage = hr_validated`. For the two scoped roles,
holding the capability is necessary but not sufficient — the caller must also be the specific
person named on *this* request (see §5 Actors, §10 Permissions / Security).
**Nonce:** required.
**Rate limit:** 30 requests / rolling hour per user — higher than `API01`'s, since a manager/HR/
receiving-manager actor may legitimately process several decisions in one session; still bounded
against scripted abuse.

**Request:**
```json
{
  "decision": "approved",
  "reason": "string — optional when decision is \"approved\"; required, max 500 characters, when decision is \"rejected\""
}
```

**Success (`200 OK`)** — example: HR approves, request moves to `hr_validated`:
```json
{
  "id": 123,
  "stage": "hr_validated",
  "decision_recorded": {
    "actor_user_id": 91,
    "prior_stage": "manager_approved",
    "decision": "approved",
    "reason": null,
    "occurred_at": "2026-09-03T14:20:00+00:00"
  },
  "pending_with_user_id": 60,
  "downstream_dispatch_fired": false
}
```

`downstream_dispatch_fired` is `true` only for the one transition `FR09`/`AC06` cares about —
`hr_validated → receiving_accepted` via an **approved** decision at the Receiving Manager stage.

**Exceptions:**

| HTTP | `error.code` | Condition | Ref |
|---|---|---|---|
| 400 | `validation_error` | `decision` missing or not one of `approved`/`rejected`, or `reason` missing while `decision = "rejected"` | — |
| 401 | `unauthorized` | No authenticated session | `ERR07` |
| 403 | `forbidden_not_a_stakeholder` | Authenticated, but is not the specific actor required for this request's current stage | `ERR06` / `AC08` |
| 404 | `transfer_not_found` | No transfer request exists with the given `{id}` | `ERR11` |
| 409 | `invalid_stage_transition` | The request is not in a decidable stage (already `receiving_accepted`, `completed`, or `rejected`) | `ERR05` |

A **rejected** decision at any stage is not itself an error condition — it returns `200 OK` with
`"stage": "rejected"` in the body, per `ERR08` (listed there precisely because Gate 1 requires the
exception table to be exhaustive, including the non-error terminal outcome, not because it fails).

## 9. Data model changes

> Conceptual only — column types, indexes, and the `dbDelta()` migration statement itself are
> authored at the Plan stage, not here. This section fixes *what fields must exist*, not *how they
> are stored*.

| Table (conceptual) | Purpose | Key fields (conceptual) |
|---|---|---|
| `wp_emp_transfers` | One row per transfer request — current state | requester user ID, target department, target location, target role, effective date, reason (nullable), current stage, current-manager user ID, HR-approver user ID, receiving-manager user ID, rejection actor/reason (nullable), created/updated timestamps |
| `wp_emp_transfer_events` | Append-only audit timeline — one row per stage transition | transfer ID (FK), actor user ID, stage transitioned to, decision (approved/rejected), optional note, timestamp |

Migration required: **yes** — deferred to the Plan stage (`dbDelta()`, plugin `db_version` bump
per `.agent/rules/int-standards.wordpress.md`).

## 10. Permissions / Security

| Concern | Rule for this spec |
|---|---|
| Capability required | See §5 Actors table — one capability per stage-action, each `current_user_can()`-checked |
| Scoping | A stage-action capability is not sufficient alone — the acting user must also be the *specific* manager/HR/receiving-manager named on *that* request (`emp-internal-transfer.AC08`) |
| Nonce | Required on every write route — `POST /transfers` (`API01`), `PATCH /transfers/{id}/approve` (`API03`) |
| PII | Employee name, ID, department/location, manager identity — never logged; masked/omitted from any error response detail |
| Downstream dispatch | `do_action('emp_internal_transfer_approved', $transfer_id, ...)` (exact hook name/payload finalised at the Plan stage) fired once per request, exactly at Stage 4 → Stage 5 |
| Audit | Every stage transition (approve, reject, accept) writes a row to `wp_emp_transfer_events` — this **is** this spec's audit trail; there is no separate audit-log feature to call into yet |

## 11. Error scenarios

| ID | Trigger | Expected HTTP / `error.code` | Notes |
|---|---|---|---|
| `emp-internal-transfer.ERR01` | Effective date < 30 days from submission | 422 / `effective_date_too_soon` | `AC01` boundary |
| `emp-internal-transfer.ERR02` | Employee tenure < 6 months | 422 / `ineligible_tenure` | `AC02` |
| `emp-internal-transfer.ERR03` | Requester already has an active transfer request | 409 / `active_transfer_exists` | `AC03` |
| `emp-internal-transfer.ERR04` | Missing/invalid required field (department, location, role) | 400 / `validation_error` | — |
| `emp-internal-transfer.ERR05` | An approval action is attempted out of stage sequence (e.g. HR decides before the Current Manager has approved) | 409 / `invalid_stage_transition` | Enforces `FR05`–`FR07`'s strict ordering |
| `emp-internal-transfer.ERR06` | Acting user holds the stage capability but is not the specific manager/HR/receiving-manager named on this request | 403 / `forbidden_not_a_stakeholder` | `AC08` |
| `emp-internal-transfer.ERR07` | Unauthenticated caller on any route | 401 / `unauthorized` | — |
| `emp-internal-transfer.ERR08` | Rejection at the Current Manager, HR, or Receiving Manager stage | 200 / n/a — not an error condition | Valid terminal outcome (`Rejected`); listed here because Gate 1 requires the exception table to be exhaustive, not because it is a failure |
| `emp-internal-transfer.ERR09` | Authenticated caller lacks the base capability for the action entirely (e.g. no `emp_transfer:submit`) | 403 / `forbidden_missing_capability` | Distinct from `ERR06` — this is "wrong capability," `ERR06` is "right capability, wrong person for this specific request" |
| `emp-internal-transfer.ERR10` | Caller exceeds a route's rate limit (`API01` or `API03`, see §8) | 429 / `rate_limited` | Numeric thresholds are provisional — `Q05` |
| `emp-internal-transfer.ERR11` | `{id}` in the route does not correspond to any transfer request | 404 / `transfer_not_found` | `API02`, `API03` |

## 12. Acceptance criteria

### `emp-internal-transfer.AC01` — Successful request initiation

```gherkin
Given an authenticated employee with at least 6 months of tenure in their current role
  And that employee has no existing transfer request in an active stage
 When the employee submits an Internal Transfer Request with a target department, a target
      location, a target role, an effective date at least 30 days in the future, and an
      optional reason
 Then the system creates a new transfer request in the "Submitted" stage
  And the request is queued for the employee's Current Reporting Manager
  And the response returns the new request's ID and its "Submitted" status
```

### `emp-internal-transfer.AC02` — Eligibility guard: tenure

```gherkin
Given an authenticated employee whose tenure in their current role/department is less than
      6 months
  And an otherwise valid transfer request payload
 When the employee submits an Internal Transfer Request
 Then the system rejects the request with `emp-internal-transfer.ERR02`
  And no transfer request record is created
```

### `emp-internal-transfer.AC03` — Concurrency guard: single active transfer

```gherkin
Given an authenticated employee who already has a transfer request in the "Submitted",
      "Manager-Approved", "HR-Validated", or "Receiving-Accepted" stage
 When that employee attempts to submit a new Internal Transfer Request
 Then the system rejects the new request with `emp-internal-transfer.ERR03`
  And the employee's existing active request is left unchanged
```

### `emp-internal-transfer.AC04` — Current manager approval & release

```gherkin
Given a transfer request in the "Submitted" stage
  And the acting user is the request's named Current Reporting Manager
 When the Current Reporting Manager approves the request
 Then the system transitions the request to the "Manager-Approved" stage
  And appends an entry to the request's audit timeline recording the manager, the decision,
      and the timestamp
  And the request becomes visible to HR for eligibility validation
```

### `emp-internal-transfer.AC05` — HR eligibility validation

```gherkin
Given a transfer request in the "Manager-Approved" stage
  And the acting user holds the HR Business Partner capability for eligibility validation
 When HR approves the request's eligibility
 Then the system transitions the request to the "HR-Validated" stage
  And appends an entry to the request's audit timeline recording the HR actor, the decision,
      and the timestamp
  And the request becomes visible to the named Receiving Department Manager
```

### `emp-internal-transfer.AC06` — Receiving manager acceptance

```gherkin
Given a transfer request in the "HR-Validated" stage
  And the acting user is the request's named Receiving Department Manager
 When the Receiving Department Manager accepts the request
 Then the system transitions the request to the "Receiving-Accepted" stage
  And appends an entry to the request's audit timeline recording the receiving manager, the
      decision, and the timestamp
  And the system fires the downstream-provisioning `do_action` dispatch to IT, Facilities,
      and Payroll exactly once
```

### `emp-internal-transfer.AC07` — Status inquiry and audit timeline visibility

```gherkin
Given a transfer request exists in any stage
  And the querying user is the request's requester, or is named as an actor at any stage of
      that specific request
 When that user requests the current status of the request
 Then the system returns the request's current stage, the identity of who it is currently
      pending with, and the complete ordered stage-by-stage audit timeline
  And the returned status reflects the request's real-time state, not a cached or stale value
```

### `emp-internal-transfer.AC08` — Authorization isolation

```gherkin
Given a transfer request belonging to a specific employee
  And the querying or acting user is neither that request's requester nor named as an actor
      at any stage of that specific request
 When that user attempts to view the request or to approve/reject/accept it
 Then the system denies the action with `emp-internal-transfer.ERR06`
  And no data belonging to that request is returned in the response body
```

## 13. Unit Test Cases (spec-derived)

One unit test per acceptance criterion, 1:1, each named so the ID is greppable back to its AC.
Layer is unit (Brain Monkey — WordPress functions mocked, no live `wp_emp_transfers` row needed).
Full QA-expanded scenarios — integration/HTTP-level coverage across all three API routes, boundary
conditions, RBAC rejection combinations, and validation failures — are maintained in
[`test_cases/emp-internal-transfer.test_cases.md`](../test_cases/emp-internal-transfer.test_cases.md),
not duplicated here.

| Test ID | Maps to | Scenario | Expected |
|---|---|---|---|
| `emp-internal-transfer.UT01` | `AC01` | Valid payload; tenure ≥ 6 months; no active request; `effective_date` exactly 30 days out | Service creates the request in `submitted` stage, sets `pending_with_user_id` to the current manager |
| `emp-internal-transfer.UT02` | `AC02` | Tenure = 5 months, 29 days (one day inside the boundary); otherwise valid payload | Service rejects with `ineligible_tenure`; no row persisted |
| `emp-internal-transfer.UT03` | `AC03` | Requester already has a request in the `manager_approved` stage | Service rejects the new request with `active_transfer_exists`; the existing row is untouched |
| `emp-internal-transfer.UT04` | `AC04` | Request in `submitted` stage; acting user is the request's named Current Reporting Manager; decision `approved` | Service transitions the request to `manager_approved` and appends a timeline entry recording the manager, decision, and timestamp |
| `emp-internal-transfer.UT05` | `AC05` | Request in `manager_approved` stage; acting user holds the HR eligibility-validation capability; decision `approved` | Service transitions the request to `hr_validated` and appends a timeline entry |
| `emp-internal-transfer.UT06` | `AC06` | Request in `hr_validated` stage; acting user is the request's named Receiving Department Manager; decision `approved` | Service transitions the request to `receiving_accepted`, appends a timeline entry, and fires the downstream `do_action` dispatch exactly once |
| `emp-internal-transfer.UT07` | `AC07` | Caller is the request's requester; request is in `manager_approved` stage with one prior timeline entry | Service returns the current stage, the pending-with actor, and the full ordered timeline (2 entries), reflecting real-time state |
| `emp-internal-transfer.UT08` | `AC08` | Caller is an authenticated user named as neither the requester nor any stage actor on this request | Service denies with `forbidden_not_a_stakeholder`; no field of the request is included in the returned data |

## 14. Performance

| Endpoint / operation class | p95 target |
|---|---|
| All routes in §8 | < 400 ms (constitution baseline) |

## 15. Coverage

**Floor:** 80% on **changed files only** (constitution).

## 16. Risks

| ID | Risk | Mitigation / owning action |
|---|---|---|
| `emp-internal-transfer.R01` | `do_action` dispatch at Stage 4→5 has no confirmed listener yet — IT/Facilities/Payroll provisioning is out of scope for this spec (§2), so the hook could fire into a void until those coordinators' own features exist | The Plan stage documents the hook contract explicitly so a future listener has something to implement against; tracked, not solved, by this spec |
| `emp-internal-transfer.R02` | No Technical Lead or Project Manager is named on the constitution's roster | Blocks a real Gate 1 review of this spec — see Author/Reviewer above and constitution.md |
| `emp-internal-transfer.R03` | `FR10`'s "Completed" acknowledgement mechanism is stated behaviourally but not designed — could be a callback hook, a manual admin action, or a timeout | Explicit technical design deferred to the Plan stage; flagged so it isn't silently decided at implementation time |
| `emp-internal-transfer.R04` | `API01`/`API03`'s numeric rate limits (10/hr, 30/hr) are the spec author's proposed defaults, not a confirmed security/TL decision | `Q05` — needs sign-off before or during Gate 1 |

## 17. Open questions

| ID | Question | Owner | Blocks Gate 1? |
|---|---|---|---|
| `emp-internal-transfer.Q01` | Carried from `BRD-001`: is the 5-day approval SLA with automatic reminders confirmed product intent? | PM (`[Open]`) → Business owner | No — resolved by keeping reminders out of scope (§2) until confirmed |
| `emp-internal-transfer.Q02` | Carried from `BRD-001`: is domestic-only transfer scope for v1 confirmed? | PM (`[Open]`) → Business owner | No — this spec is silent on cross-border handling rather than guessing at a guard |
| `emp-internal-transfer.Q03` | Should a rejection at any stage permit the employee to resubmit immediately, or is there a cooldown period? | PM (`[Open]`) | No — `FR04`'s "active" definition already makes `Rejected` non-active, so immediate resubmission is the current behaviour by default; flagged in case that is not the intended policy |
| `emp-internal-transfer.Q04` | Who is the Technical Lead and Project Manager for this project? | Business owner | **Yes** — a real Gate 1 decision cannot be recorded without a named reviewer |
| `emp-internal-transfer.Q05` | Are `API01`'s 10/hour and `API03`'s 30/hour rate limits acceptable, or does security/TL want different thresholds? | TL (`[Open]`) | No — a reasonable default is in place (`NFR06`); this only needs confirmation, not resolution, to proceed |

## 18. Traceability

| Artefact | Reference |
|---|---|
| BRD | `BRD-001` |
| ADR | none |
| Related specs | none — first feature on this portal |
| Plan | `.ai-context/plans/emp-internal-transfer.plan.md` (not yet authored — after Gate 1 Approves) |
| Tasks | `.ai-context/tasks/emp-internal-transfer.tasks.md` (not yet authored) |
| Tests | §13 (spec-derived, UT01–UT08) and [`test_cases/emp-internal-transfer.test_cases.md`](../test_cases/emp-internal-transfer.test_cases.md) (QA-expanded) — no test *code* written yet, per test-first discipline |

## 19. Review checklist (Gate 1)

Reviewer confirms each box before **Approved**:

- [ ] Business objective is clear and matches the BRD citation
- [ ] Scope / out of scope prevents adjacent-slug creep
- [ ] Dependencies and blocking BRDs/ADRs are honest
- [ ] Every owned HTTP route has an API contract entry (or explicit "no HTTP") — all three routes
      fully specified in §8 as of this Day 3 revision
- [ ] Acceptance criteria are given/when/then and IDed
- [ ] Error scenarios are exhaustive for the contract, not happy-path-only — §11 now includes
      `ERR09`–`ERR11`, added to cover codes introduced by the finalized §8 contract
- [ ] Performance and coverage expectations are named and match the constitution
- [ ] Capability, nonce, PII, and rate-limit decisions are explicit — rate limits are set (§8) but
      numerically provisional pending sign-off (`Q05`)
- [ ] Risks reference `status.md` engineering risks where applicable
- [ ] Open questions that block Gate 1 are none — or escalated before approval — *`Q04` currently
      blocks a real Gate 1 decision; see Author/Reviewer above*
- [ ] Author ≠ Gate 1 reviewer
- [ ] Traceability table is complete

**Gate 1 decision:** Pending · **Date:** — · **Reviewer:** `[Open]`
