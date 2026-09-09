# Test Cases: Employee Internal Transfer Journey

**Spec:** [`specs/emp-internal-transfer.spec.md`](../specs/emp-internal-transfer.spec.md)
(`In Peer Review (Gate 1)`) · **BRD:** [`BRD-001`](../BRD.md) · **Last updated:** 2026-08-31

QA/developer-expanded scenarios derived from the spec's Acceptance Criteria (§12), API Contract
(§8), and Error scenarios (§11). The spec's own §13 stays a compact 1:1 `UT01`–`UT08` ↔ `AC01`–
`AC08` traceability table; this file is where the exhaustive scenario detail — happy path,
boundaries, edge cases, RBAC rejections, validation failures — actually lives, per
`.agent/workflows/generate-tests.md`. No test code exists yet: these are the scenarios test-first
implementation will turn into PHPUnit tests, confirmed Red before any handler is written.

---

## 1. Happy path

| Test ID | Maps to | Scenario | Preconditions | Expected result |
|---|---|---|---|---|
| `TC-HP-01` | `AC01`, `API01` | Submit a valid transfer request | Tenure ≥ 6 months; no active request; `effective_date` ≥ 30 days out | `201 Created`, `stage: "submitted"`, `pending_with_user_id` = current manager |
| `TC-HP-02` | `AC04`, `API03` | Current manager approves a submitted request | Request in `submitted`; actor is the named current manager | `200 OK`, `stage: "manager_approved"` |
| `TC-HP-03` | `AC05`, `API03` | HR validates a manager-approved request | Request in `manager_approved`; actor holds `emp_transfer:validate_hr` | `200 OK`, `stage: "hr_validated"` |
| `TC-HP-04` | `AC06`, `API03` | Receiving manager accepts an HR-validated request | Request in `hr_validated`; actor is the named receiving manager | `200 OK`, `stage: "receiving_accepted"`, `downstream_dispatch_fired: true` |
| `TC-HP-05` | `AC07`, `API02` | Requester checks status mid-flow | Request in `manager_approved` | `200 OK`, current stage, pending-with actor, ordered timeline (2 entries) |
| `TC-HP-06` | full journey | End-to-end: submit → manager-approve → HR-validate → receiving-accept | Fresh employee, eligible | Final `stage: "receiving_accepted"`; timeline has exactly 4 ordered entries; `do_action` dispatch fires **exactly once**, only at the last transition |

## 2. Boundary checks

| Test ID | Maps to | Scenario | Expected result |
|---|---|---|---|
| `TC-BD-01` | `AC01`, `ERR01` | `effective_date` = today + 29 days (one day short of the limit) | `422 effective_date_too_soon` |
| `TC-BD-02` | `AC01` | `effective_date` = today + 30 days exactly | `201 Created` — the 30-day boundary is inclusive |
| `TC-BD-03` | `AC01`, `ERR01` | `effective_date` = today (0 days out) | `422 effective_date_too_soon` |
| `TC-BD-04` | `API01` | `reason` at exactly 500 characters | `201 Created` |
| `TC-BD-05` | `API01`, `ERR04` | `reason` at 501 characters | `400 validation_error` |
| `TC-BD-06` | `API01` | `reason` omitted entirely (it is optional) | `201 Created`, `reason: null` |

## 3. Tenure edge cases

| Test ID | Maps to | Scenario | Expected result |
|---|---|---|---|
| `TC-TN-01` | `AC02`, `ERR02` | Tenure = 5 months, 29 days | `422 ineligible_tenure` |
| `TC-TN-02` | `AC01` | Tenure = exactly 6 months | `201 Created` — the 6-month boundary is inclusive |
| `TC-TN-03` | `AC01` | Tenure = 6 months, 1 day | `201 Created` |
| `TC-TN-04` | `AC02` | Tenure = 0 (day-one new hire) | `422 ineligible_tenure` |
| `TC-TN-05` | `AC02` | Tenure record missing/null (data-integrity edge, not a business scenario) | Fail closed — treated as ineligible, not silently allowed; flagged as an implementation note for the Plan stage, not resolved here |

## 4. Concurrency / single-active-transfer guard

| Test ID | Maps to | Scenario | Expected result |
|---|---|---|---|
| `TC-CC-01` | `AC03`, `ERR03` | Existing request in `submitted`; new submission attempted | `409 active_transfer_exists` |
| `TC-CC-02` | `AC03` | Existing request in `manager_approved`; new submission attempted | `409` |
| `TC-CC-03` | `AC03` | Existing request in `hr_validated`; new submission attempted | `409` |
| `TC-CC-04` | `AC03`, `FR04` | Existing request in `receiving_accepted` (pre-completion); new submission attempted | `409` — still "active" per `BRD-001`'s non-completed/non-rejected definition |
| `TC-CC-05` | `FR04` | Existing request in `Rejected`; new submission attempted | `201 Created` — `Rejected` is not active; immediate resubmission is today's behaviour (see spec `Q03` on whether a cooldown is actually wanted) |
| `TC-CC-06` | `FR04` | Existing request in `Completed`; new submission attempted | `201 Created` |

## 5. Sequential-approval / stage-transition guard

`403 forbidden_not_a_stakeholder` = wrong actor for a **still-decidable** stage. `409
invalid_stage_transition` = **no** actor can decide, because the stage is already fully processed.
These are deliberately distinct codes for distinct conditions — do not collapse them.

| Test ID | Maps to | Scenario | Expected result |
|---|---|---|---|
| `TC-SEQ-01` | `ERR06` | HR-capable user attempts a decision while the request is still in `submitted` (awaiting the current manager) | `403 forbidden_not_a_stakeholder` |
| `TC-SEQ-02` | `ERR06` | Named receiving manager attempts a decision while the request is in `manager_approved` (awaiting HR) | `403 forbidden_not_a_stakeholder` |
| `TC-SEQ-03` | `ERR06` | Current manager attempts a second decision after the request has already moved to `hr_validated` | `403 forbidden_not_a_stakeholder` |
| `TC-SEQ-04` | `ERR05` | Any actor attempts a decision on a request already in `receiving_accepted` | `409 invalid_stage_transition` |
| `TC-SEQ-05` | `ERR05` | Any actor attempts a decision on a `Completed` request | `409 invalid_stage_transition` |
| `TC-SEQ-06` | `ERR05` | Any actor attempts a decision on a `Rejected` request | `409 invalid_stage_transition` |
| `TC-SEQ-07` | `AC04`, `ERR08` | Current manager rejects a `submitted` request | `200 OK`, `stage: "rejected"` |
| `TC-SEQ-08` | `AC05`, `ERR08` | HR rejects a `manager_approved` request | `200 OK`, `stage: "rejected"` |
| `TC-SEQ-09` | `AC06`, `ERR08` | Receiving manager rejects an `hr_validated` request | `200 OK`, `stage: "rejected"`, `downstream_dispatch_fired: false` |

## 6. RBAC / authorization-isolation (security) rejections

| Test ID | Maps to | Scenario | Expected result |
|---|---|---|---|
| `TC-RBAC-01` | `AC08`, `ERR06` | An authenticated employee with no relation to the request calls `GET /transfers/{id}` on someone else's request | `403 forbidden_not_a_stakeholder` |
| `TC-RBAC-02` | `AC08`, `ERR06` | Same unrelated employee calls `PATCH /transfers/{id}/approve` on someone else's request | `403 forbidden_not_a_stakeholder` |
| `TC-RBAC-03` | `ERR09` | Authenticated user without `emp_transfer:submit` calls `POST /transfers` | `403 forbidden_missing_capability` |
| `TC-RBAC-04` | clarifies §5 Actors | An HR-capable user who is **not** named as the current manager or receiving manager on a request still successfully validates it at the `manager_approved` stage | `200 OK` — confirms the HR capability is portal-wide, not scoped per-request, unlike the current-manager/receiving-manager checks |
| `TC-RBAC-05` | `AC08`, `ERR06` | A manager who **is** someone's current manager, but not the current manager **named on this specific request**, attempts to approve it | `403 forbidden_not_a_stakeholder` |
| `TC-RBAC-06` | `ERR07` | Unauthenticated caller (no session/cookie) calls any of the three routes | `401 unauthorized` on all three |

## 7. Validation failures

| Test ID | Maps to | Scenario | Expected result |
|---|---|---|---|
| `TC-VAL-01` | `ERR04` | `POST /transfers` missing `target_department` | `400 validation_error` |
| `TC-VAL-02` | `ERR04` | `POST /transfers` missing `target_location` | `400 validation_error` |
| `TC-VAL-03` | `ERR04` | `POST /transfers` missing `target_role` | `400 validation_error` |
| `TC-VAL-04` | `ERR04` | `POST /transfers` missing `effective_date` | `400 validation_error` |
| `TC-VAL-05` | `ERR04` | `POST /transfers` `effective_date` not a valid ISO 8601 date (e.g. `"15-10-2026"`) | `400 validation_error` |
| `TC-VAL-06` | `API03` | `PATCH .../approve` missing `decision` | `400 validation_error` |
| `TC-VAL-07` | `API03` | `PATCH .../approve` `decision: "maybe"` (not `approved`/`rejected`) | `400 validation_error` |
| `TC-VAL-08` | `API03` | `PATCH .../approve` `decision: "rejected"` with no `reason` | `400 validation_error` — `reason` is required on rejection |

## 8. Rate limiting

| Test ID | Maps to | Scenario | Expected result |
|---|---|---|---|
| `TC-RATE-01` | `ERR10`, `API01` | 11th `POST /transfers` by the same user inside a rolling hour | `429 rate_limited` |
| `TC-RATE-02` | `ERR10`, `API03` | 31st `PATCH .../approve` by the same actor inside a rolling hour | `429 rate_limited` |
| `TC-RATE-03` | `API02` | Repeated `GET /transfers/{id}` polling (e.g. 100/hour) by the requester | Never rate-limited — §8 states none for the read route |

## 9. Not-found

| Test ID | Maps to | Scenario | Expected result |
|---|---|---|---|
| `TC-NF-01` | `ERR11`, `API02` | `GET /transfers/{id}` with an `{id}` that does not exist | `404 transfer_not_found` |
| `TC-NF-02` | `ERR11`, `API03` | `PATCH /transfers/{id}/approve` with an `{id}` that does not exist | `404 transfer_not_found` |

## 10. PII / logging (non-functional verification)

| Test ID | Maps to | Scenario | Expected result |
|---|---|---|---|
| `TC-PII-01` | `NFR02` | Trigger every error path in §1–9 above with `WP_DEBUG_LOG` enabled | No employee name, ID, email, department/location-identifying detail, or manager identity appears in the log output in plaintext, on any path |
| `TC-PII-02` | `NFR02` | Run the full happy-path journey (`TC-HP-06`) with logging enabled | Same — zero PII in logs even on success paths |

---

## Coverage summary

| Section | Scenario count |
|---|---|
| Happy path | 6 |
| Boundary checks | 6 |
| Tenure edge cases | 5 |
| Concurrency guard | 6 |
| Sequential-approval guard | 9 |
| RBAC / authorization isolation | 6 |
| Validation failures | 8 |
| Rate limiting | 3 |
| Not-found | 2 |
| PII / logging | 2 |
| **Total** | **53** |

Every `AC01`–`AC08`, `ERR01`–`ERR11`, and all three API routes (`API01`–`API03`) have at least one
scenario above. This file is expanded and maintained alongside the spec — a spec revision that
changes an AC, FR, or error code must update the corresponding row here in the same task.
