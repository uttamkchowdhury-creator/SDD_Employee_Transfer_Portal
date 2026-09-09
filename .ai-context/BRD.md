# BRD — Business Requirements

Where a requirement is **first written down**, before it becomes a spec. A spec must never be the
first place a requirement appears. Entries are numbered, never renumbered, and never deleted —
superseded entries are marked, not removed.

> **Reset notice (2026-08-29).** This file previously held BRD-001 through BRD-012 for an
> unrelated Node.js project (Empty Floor + Circle Tap). That content has been fully replaced.
> Numbering restarts at BRD-001 for the **One-Point Employee Portal**.

**Owner:** Developer (acting Spec Author/PM for this Discovery pass — a dedicated PM is
**[Open]**, see [constitution.md](constitution.md)) · **Status legend:** `Open` · `Decided` ·
`Superseded`

---

### BRD-001: Employee Internal Transfer Digital Journey

**Raised by:** Internal discovery request, 2026-08-29
**Business Problem:** Internal employee transfers across departments and/or locations are
currently handled through ad hoc email threads, manual approval chains, and spreadsheet-tracked
handoffs to downstream teams (IT, Facilities, Payroll). This produces no single source of truth
for where a transfer request stands, inconsistent application of eligibility rules, and no audit
trail of who approved what and when.

**Objective:** Digitize the internal transfer request lifecycle end-to-end inside the One-Point
Employee Portal, and provide single-pane orchestration tracking so any actor in the chain — and
HR overall — can see a transfer's current stage, pending owner, and history without asking around.

**Primary Actors:**

| Actor                                             | Role in this journey                                                 |
| ------------------------------------------------- | -------------------------------------------------------------------- |
| Requester (Employee)                              | Initiates the transfer request                                       |
| Current Reporting Manager                         | First approval gate — releases the employee from their current team  |
| HR Business Partner                               | Validates eligibility against policy (tenure, active-transfer limit) |
| Receiving Department Manager                      | Accepts the employee into the new department/location                |
| Downstream Coordinators (IT, Facilities, Payroll) | Execute provisioning/de-provisioning once the transfer is approved   |

**Digital Journey Stages:**

1. **Request Initiation** — Requester submits a transfer request (target department, location,
   effective date, reason).
2. **Current Manager Approval** — Current Reporting Manager approves or rejects the release.
3. **HR Eligibility Validation** — HR Business Partner checks the request against policy rules
   (see Business Decisions below) and approves or rejects.
4. **Receiving Manager Acceptance** — Receiving Department Manager accepts or declines the
   incoming employee.
5. **Downstream Provisioning & Updates** — On full approval, IT/Facilities/Payroll are notified
   asynchronously to provision access, workspace, and compensation/reporting-line updates.
6. **Completion** — Transfer is marked complete once all downstream provisioning steps are
   acknowledged; the requester and both managers are notified.

**Business Decisions vs Technical Decisions**

_Business decisions:_

- **6-month tenure eligibility rule** — an employee must have at least 6 months of tenure in
  their current role/department before an internal transfer request is eligible.
- **Mandatory sequential approval hierarchy** — stages 2→3→4 above must occur in strict order; a
  later-stage approver cannot act before an earlier stage has approved.
- **Single active transfer limit** — an employee may have at most one active (non-completed,
  non-rejected) transfer request in flight at any time.

_Technical decisions:_

- Custom WordPress plugin structure at `/wp-content/plugins/emp-internal-transfer/`.
- Custom REST namespace `/wp-json/one-point/v1/transfers`.
- Custom database table `wp_emp_transfers` (via `$wpdb`, not posts/postmeta).
- Hook-based asynchronous dispatch (`do_action`) to downstream coordinators (IT, Facilities,
  Payroll) rather than a queue or message broker.

**Open Ambiguities & Assumptions:**

- **5-day approval SLA** with automatic email reminders is assumed for each approval stage
  (Current Manager, HR, Receiving Manager) — not yet confirmed with a business owner. Blocks any
  spec section defining reminder/escalation behaviour until confirmed.
- **Domestic-only transfers for v1** — cross-location transfers are assumed to be within-country
  only for the initial release; cross-border transfers are out of scope (see below) but the
  domestic-only assumption itself has not been explicitly confirmed by a business owner.

**Explicitly Out of Scope:**

- Cross-border tax compliance implications of a transfer.
- Inter-entity legal contract changes (e.g. transfers that would move an employee between
  separate legal entities).

**Status:** Open — pending confirmation of the SLA and domestic-only assumptions above, and
pending a named PM/business owner to formally sign off on this BRD before a spec is authored
against it.

---

## Open-item register

| Ref     | Open question                                                                     | Owner                     | Blocks                                                                 |
| ------- | --------------------------------------------------------------------------------- | ------------------------- | ---------------------------------------------------------------------- |
| BRD-001 | 5-day approval SLA + auto-reminders — confirmed intent or placeholder assumption? | PM (TBD) → Business owner | Approval-stage reminder/escalation behaviour in the eventual spec      |
| BRD-001 | Domestic-only transfers for v1 — confirmed scope boundary?                        | PM (TBD) → Business owner | Cross-border handling stays correctly out of scope only once confirmed |
| BRD-001 | No PM or Technical Lead is yet named on this project                              | TBD                       | Gate 1 / Gate 2 review of any future spec derived from this BRD        |
