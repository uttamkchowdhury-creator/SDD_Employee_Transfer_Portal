# Source Document Registry

Requirement and standard sources for the **One-Point Employee Portal**. Unlike the previous
project this workspace was cloned from, both source documents here are small enough to live in the
repository directly (`docs/`) rather than external shared storage — this registry exists purely
for traceability (which revision a spec was written from), not to justify externalising them.

> **Reset notice (2026-08-29).** This registry previously tracked four PDFs for an unrelated
> project (Empty Floor + Circle Tap FRS, buyer/broker journeys, business proposal). Those documents
> and `proposal-extract.md` have been deleted from `source-docs/` — none of it applies to this
> project.

**Registry owner:** Developer (shamik.bhattacharya@intglobal.com) · **Last verified:** 2026-08-29

---

## Registry

| ID          | Document                                                              | Location                                                                               | Version            | Size   | SHA-256                                                            |
| ----------- | --------------------------------------------------------------------- | -------------------------------------------------------------------------------------- | ------------------ | ------ | ------------------------------------------------------------------ |
| **STD-SDD** | INT Engineering Guidelines — Specification-Driven Delivery            | [`docs/INT SDD BluePrint - V1.0.pdf`](../../docs/INT%20SDD%20BluePrint%20-%20V1.0.pdf) | v1.0 (24-Jul-2026) | 2.2 MB | `d5573335bbb2073a6f58298291d110be27b6341311acb466748efe573e9e7a99` |
| **REQ-SDD** | SDD Developer Assessment — Employee Internal Transfer Digital Journey | [`docs/Requirement for SDD.docx`](../../docs/Requirement%20for%20SDD.docx)             | as received        | 12 KB  | `838600decc5f116a567d95fdab395c166be697ca3cc30d8b5205de9464bb988e` |

**REQ-SDD** is the primary requirement source for `BRD-001` (see [`../BRD.md`](../BRD.md)) — it
states the business context (existing manual internal-transfer process across manager, HR,
payroll, IT, and facilities), the business requirement (self-service request initiation with
status/pending-action visibility), and the assessment's expected SDD artefact chain and milestones.
**STD-SDD** is the process standard this whole repository operates under (`CLAUDE.md`,
`constitution.md`).

## What stayed vs. what changed from the previous registry

The prior registry externalised its four PDFs to shared storage because they totalled 25 MB and
one of them (a business proposal) was actively misleading — its content described a different
engagement. Neither problem applies here: both documents together are ~2.2 MB, both are the real,
correct source for this project, and there is no size or misdirection reason to keep them out of
the repository. They are checked in as `docs/*.pdf` / `docs/*.docx`.

## Revision protocol

If either source document is revised:

1. Record the new version and checksum as a **new row**; keep the old row and mark it superseded.
   Do not overwrite history.
2. Raise a BRD entry for any changed requirement (`BRD.md`) — a document revision is not, by
   itself, authority to change a spec; the BRD → Spec → Plan → Tasks chain still applies.
3. Note the revision in `status.md`'s Daily Execution Log the same day.
