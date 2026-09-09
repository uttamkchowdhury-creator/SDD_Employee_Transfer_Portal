# Project Context — One-Point Employee Portal

One-page orientation. What you would hand a new engineer on day one, before anything else.

> **Reset notice (2026-08-29).** This file previously described an unrelated Node.js/TypeScript
> project (Empty Floor + Circle Tap). That content has been fully replaced. This workspace now
> runs Discovery for the **One-Point Employee Portal**, starting with the **Employee Internal
> Transfer Journey** (`emp-internal-transfer`).

## Objective

**One-Point Employee Portal** is the centralized self-service gateway through which employees
initiate and track internal HR/operational workflows, replacing scattered email threads, manual
approval chains, and spreadsheet-tracked handoffs with one auditable, workflow-driven system built
as a custom WordPress plugin.

The first digital journey onboarded onto this portal is **internal employee transfers** — moving
an employee between departments and/or locations through a structured, multi-stage approval and
provisioning pipeline. See [BRD.md](BRD.md) (`BRD-001`) for the full business requirement.

## Architecture in one paragraph

A custom WordPress plugin (`/wp-content/plugins/emp-internal-transfer/`) exposes a versioned WP
REST API namespace (`/wp-json/one-point/v1/transfers`) backed by a dedicated custom table
(`wp_emp_transfers`) accessed via `$wpdb` — WordPress MySQL is the system of record, not
posts/postmeta. REST controllers extend `WP_REST_Controller` and stay mapping-only; business rules
(eligibility, sequential approval routing, single-active-transfer enforcement) live in service
classes; `$wpdb` access is isolated to repository classes. Downstream provisioning notifications to
IT, Facilities, and Payroll are dispatched asynchronously via WordPress Action Hooks (`do_action`),
not a queue or message broker. See [architecture.md](architecture.md) for the intended stack —
nothing is built yet, so it currently documents constraints, not an as-built system.

## Stakeholders

| Role                                            | Person                     |
| ----------------------------------------------- | -------------------------- |
| Technical Lead / Architect / constitution owner | **[Open]** — not yet named |
| Spec Author (this session)                      | Developer                  |
| Project Manager (BRD / product sign-off)        | **[Open]** — not yet named |
| Client / Business owner                         | **[Open]** — not yet named |

## How work happens here

This project runs **Specification-Driven Delivery (SDD) v1.0**. Nothing is implemented by
prompting against the codebase directly.

```
BRD → Spec → Gate 1 (Spec Review) → Plan → Architecture check → Tasks
    → Test-first (Red) → Guided implementation (Green) → Gate 2 → Merge → Release
```

Start here, in order:

1. [constitution.md](constitution.md) — the non-negotiables. Read before writing any plan.
2. [BRD.md](BRD.md) — where a requirement is first written down, with open decisions listed.
3. [architecture.md](architecture.md) — the living system design (currently a placeholder —
   nothing is built yet).
4. [status.md](status.md) — what is in flight right now.
5. `.agent/rules/int-standards.wordpress.md` — always-on coding rules for this stack.

Supporting references:

- [source-docs/README.md](source-docs/README.md) — registry of this project's two real source
  documents (the SDD blueprint and the requirement brief), both checked into `docs/`.

Per-feature artefacts live in `specs/`, `plans/`, `tasks/`, `test_cases/`, keyed by slug.
Decisions live in `decisions/ADR-NNNN-*.md`. Releases live in `releases/`. `inventory/` is empty —
this is a greenfield plugin with no existing code to inventory, unlike a retro-spec back-fill. All
of these are currently empty — this repository has not yet authored a spec for this feature.

## Current state

**Discovery.** BRD-001 (Employee Internal Transfer Digital Journey) has just been authored. No
spec, plan, or tasks file exists yet for `emp-internal-transfer` — per the constitution, none may
be written until BRD-001 is reviewed and a spec is drafted from it, gated, and approved. See
[status.md](status.md) for the active feature row and daily execution log.

## Operational notes

No build, run, or test tooling has been established in this workspace for the WordPress stack yet
— this is Discovery only. Setup, run, and deploy instructions will be added to `README.md` once the
plugin scaffold exists.
