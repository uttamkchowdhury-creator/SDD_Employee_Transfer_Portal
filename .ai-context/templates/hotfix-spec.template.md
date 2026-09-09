# Spec: HOTFIX-<incident-slug>

<!-- Filename: .ai-context/specs/hotfix-<YYYY-MMDD>-<incident-slug>.spec.md
     Hotfixes get a compressed chain, never a skipped one. -->

## Status
Emergency-Merged → Retro-Documented (same day)

## Related
.ai-context/specs/<original-feature-slug>.spec.md

## Incident
**Hotfix ID:** HOTFIX-<YYYY-MMDD>-<incident-slug> · **Detected:** <when/how> · **Severity:** <P1/P2>

## What Was Broken
<One paragraph, in behavioural terms.>

## Root Cause
<One paragraph — the cause, not the symptom. Written before the patch, not after.>

## Correct Behaviour (Acceptance Criteria)
1. `<hotfix-slug>.AC1` — <the failing scenario now succeeds>
2. `<hotfix-slug>.AC2` — <the existing regression suite still passes>

## Fix Summary
<What changed, and why it respects constitution.md.>

## Classification
- [ ] Spec gap — the original spec was ambiguous or silent (original spec gets updated)
- [ ] Implementation defect — the spec was fine
- [ ] Occurrence count in this area: <n> — **3rd occurrence triggers an ADR, not another patch**

## Process compliance
- [ ] Lightweight spec written **before** the patch
- [ ] Gate 1 reviewer-not-author rule waived only for a genuine production emergency
- [ ] Gate 2 **not skipped** — reviewed within 24 hours regardless
- [ ] Security checklist re-run
- [ ] Status moved to Retro-Documented the same working day
- [ ] `status.md` Daily Execution Log entry added
- [ ] Included in the next release notes
