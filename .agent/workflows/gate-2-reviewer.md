# Gate 2 Reviewer — Complete Brief

**Project:** One-Point Employee Portal
**Audience:** Gate 2 reviewer (default: the constitution's Technical Lead — currently `[Open]`,
not yet named)
**Sources:** INT SDD Blueprint v1.0 (`docs/INT SDD BluePrint - V1.0.pdf`), project constitution,
`.agent/workflows/code-review.md`
**Last updated:** 2026-08-29

---

## 1. Role

|                    |                                                                                                 |
| ------------------ | ----------------------------------------------------------------------------------------------- |
| **Who**            | Technical Lead (`[Open]` — not yet named) — default Gate 2 reviewer                             |
| **Question**       | Did we build what the _approved_ spec said, with evidence?                                      |
| **Not your job**   | Re-judge whether the spec/plan is the right thing to build (that is Gate 1 / the PM's question) |
| **Accountability** | A human owns every merged line. "The agent wrote it" is never a valid defence.                  |

Companion workflow for the short prompt: [code-review.md](./code-review.md).

---

## 2. Before you open the diff (scope understanding)

Read only — do **not** re-litigate the spec:

1. Approved spec — `.ai-context/specs/<slug>.spec.md`
2. Approved plan — `.ai-context/plans/<slug>.plan.md` (especially **Explicitly Deferred**)
3. Tasks — `.ai-context/tasks/<slug>.tasks.md`
4. Test cases — `.ai-context/test_cases/<slug>.test_cases.md`
5. Constitution — `.ai-context/constitution.md`
6. Stack rules — `.agent/rules/int-standards.wordpress.md`
7. ADRs / `architecture.md` — only if the plan or diff touches them

You need a clear understanding of: feature objective, scope, each AC by ID, constraints,
dependencies, and what was **deferred**.

---

## 3. Gate 2 checklist (required)

### A. Acceptance criteria

- [ ] Every AC verified **individually by ID** against the diff — not "looks reasonable"
- [ ] Record evidence per AC (test name, controller method, or behaviour)
- [ ] Never approve on overall behaviour alone

**Suggested evidence table (optional but recommended):**

| AC ID | Description | Evidence                         | Result      |
| ----- | ----------- | -------------------------------- | ----------- |
| AC1   | …           | Code + Test                      | Pass / Fail |
| AC2   | …           | REST response / integration test | Pass / Fail |

### B. Scope / plan fidelity

- [ ] Nothing the plan **explicitly deferred** was built
- [ ] Implementation matches approved REST contract (status codes, error shapes, schema)
- [ ] No silent product redesign at review time

### C. Test discipline

- [ ] Tests written **first**, confirmed **Red**, then **Green** (not retrofitted)
- [ ] Unit (Brain Monkey) + integration (`WP_UnitTestCase`) coverage for owned behaviour
- [ ] Negative / edge / error cases present (ERR\* where the spec defines them)
- [ ] Test IDs map back to ACs (`.ai-context/test_cases/<slug>.test_cases.md`)
- [ ] Coverage floor met — 80% line coverage on changed files (constitution)

### D. Constitution & stack compliance

- [ ] Security Posture respected in _code_ (not only named in the spec)
- [ ] Architectural constraints respected (`$wpdb` as system of record, layering, `do_action`
      dispatch, no new datastore/queue without ADR)
- [ ] Logging / PII / performance / naming / layering per constitution +
      `int-standards.wordpress.md`
- [ ] Plan silence on a constitution rule is **not** an excuse if the code violates it

### E. Security (same weight as functional correctness)

- [ ] No PII in logs at any level — employee name, ID, contact detail, department/location, and
      manager identity never reach `error_log()`/`WP_DEBUG_LOG` in plaintext
- [ ] No secrets, credentials, or tokens hardcoded or logged
- [ ] Every route's `permission_callback` uses `current_user_can()` (or a mapped capability),
      never `__return_true` for a state-changing or PII-bearing route
- [ ] Nonce verification present on every authenticated write path
- [ ] Every input sanitized at the boundary; every output escaped
- [ ] Every `$wpdb` query touching external input uses `$wpdb->prepare()`
- [ ] Every new/changed REST route has an **explicit rate-limit decision**
- [ ] Auth boundary and capability gate **checked**, not assumed
- [ ] New dependencies vetted before entering the manifest
- [ ] `$wpdb` confined to repository classes — no queries in controllers
- [ ] Hot-path queries checked for missing indexes on the custom table
- [ ] SAST/dependency scan clean, or exceptions signed off

### F. Code quality

- [ ] Readability, naming, dead code, duplication
- [ ] Error handling and edge/failure paths (agents get happy path; you own failure paths)
- [ ] Consistency with existing plugin patterns
- [ ] Production performance concerns that unit tests miss

### G. Documentation currency (standing Gate 2 duty)

- [ ] Spec **Status** updated
- [ ] `.ai-context/test_cases/<slug>.test_cases.md` current
- [ ] `architecture.md` / ADR updated **if the change warrants it**
- [ ] `.ai-context/status.md` updated **same day**
- [ ] `prompt_history.md` reflects the work (via auto-log discipline)

### H. Repository hygiene

- [ ] Correct branch: `feature/<slug>` (or `fix/` / `hotfix/` per convention)
- [ ] PR reviewable in one sitting against this spec's ACs (split if not)
- [ ] Task IDs in commits (e.g. `Implements <slug>.T03`) — **traceability, not AI attribution**
- [ ] **No** AI attribution in comments or commit messages
- [ ] Merge target / process: **`[Open]`** — not yet decided for this repository
- [ ] Folder structure / artefact layout respected (`/wp-content/plugins/<slug>/`)

### I. This project's architecture traps

- [ ] A `permission_callback` without a paired nonce check is a CSRF gap even when the capability
      check is correct.
- [ ] `do_action()` dispatch with no registered listener fails silently — verify a listener
      actually exists.
- [ ] A controller running `$wpdb` queries directly instead of delegating to a repository class
      is a layering violation.

### J. Green commands

- [ ] `phpcs` (WPCS ruleset, zero errors)
- [ ] PHPUnit suite
- [ ] Scoped coverage command for the changed files

---

## 4. Decision outcomes

| Decision                                      | Meaning                             |
| --------------------------------------------- | ----------------------------------- |
| **Approved**                                  | Ready to merge                      |
| **Approved with minor comments / Should-Fix** | Non-blocking; close-out tracked     |
| **Changes Requested**                         | Must fix before merge               |
| **Rejected**                                  | Significant issues; rework required |

Categorise every finding:

- **Blocking** — blocks merge
- **Should-Fix** — required soon; may allow Approve with close-out
- **Nit** — optional / informational

For each finding: cite **rule, AC ID, ERR ID, or constitution line**. Do not restate what the code
does.

Record on the spec (**Gate 2 decision** line): decision, date, reviewer, Blocking / Should-Fix /
Nits.

---

## 5. Prompt template

```
Review the diff for <slug>.<task-id>  (or full slug for Gate 2 close-out).

Read:
- .ai-context/specs/<slug>.spec.md          (verify each AC by ID)
- .ai-context/plans/<slug>.plan.md          (check nothing deferred was built)
- .ai-context/constitution.md               (security posture, architectural constraints)
- .agent/rules/int-standards.wordpress.md
- .agent/workflows/code-review.md
- .agent/workflows/gate-2-reviewer.md

Verify Gate 2 checklist + security checklist.
Report: Blocking / Should-Fix / Nit — cite rule or AC ID per finding.
Do not re-litigate the spec (Gate 1). Do not restate what the code does.
```

---

## 6. Explicitly out of Gate 2 (do not do these)

These are **Gate 1** (PM, `[Open]`) / plan review:

- Is the business objective / BRD alignment right?
- Are ACs well-phrased (given/when/then) or complete as product intent?
- Is out-of-scope complete as a _product_ decision?
- Does the REST contract _should_ be this shape? (vs. "code matches the approved contract")
- Spec overlap / dependency specs Approved?
- Author ≠ Gate 1 reviewer on the _spec_
- Plan constitution check _before_ tasks (already done at Gate 1)
- Re-opening Open Questions that Gate 1 marked non-blocking

**Allowed grey-area (still Gate 2):** "As-built matches the _already approved_ contract" —
documentation fidelity, not redesign.

---

## 7. Definition of Done (implementation → merge)

All of these must be true before you **Approve** for merge:

1. All ACs verified by ID
2. Tests Red → Green
3. No AI attribution
4. No secrets/PII in artefacts or logs
5. Security checklist passed
6. `architecture.md` / ADR updated if warranted
7. Gate 2 complete; categorised feedback addressed
8. `test_cases/<slug>.test_cases.md` + spec Status updated
9. `status.md` updated same day

---

## 8. Quick reference — Gate 2 vs Gate 1

| Gate       | Question                                                  | Owner (this project) |
| ---------- | --------------------------------------------------------- | -------------------- |
| **Gate 1** | Is this the right thing, scoped and traceable to the BRD? | PM (`[Open]`)        |
| **Gate 2** | Is it built right, with evidence?                         | TL (`[Open]`)        |

Gate 1 requires **recorded TL technical concurrence** when a spec touches Security Posture or
Architectural Constraints (constitution rule). That concurrence is still a Gate 1 artefact, not a
substitute for Gate 2.

---

_End of Gate 2 Reviewer Brief_
