# Architecture — One-Point Employee Portal

Living system design. **Must stay current** — a stale architecture document actively misleads the
next agent session, which is worse than no document. Update this file whenever a plan introduces a
new integration, datastore, or significant decision. Currency is a standing Gate 2 checklist item.

> **Reset notice (2026-08-29).** This file previously described an unrelated Node.js/TypeScript
> project (Empty Floor + Circle Tap), inferred from a running codebase. That content has been fully
> removed. There is no running codebase for the One-Point Employee Portal yet — this is a
> **greenfield plugin**, still in Discovery. This file is a placeholder until a plan introduces the
> first real component.

---

## 1. Runtime and stack (intended, per constitution — nothing built yet)

| Concern        | Choice                                     | Notes                                                             |
| -------------- | ------------------------------------------ | ----------------------------------------------------------------- |
| Platform       | WordPress (custom plugin)                  | See [BRD-001](BRD.md) and [constitution.md](constitution.md)      |
| Language       | PHP 8.x, strict typing                     | `declare(strict_types=1);` everywhere                             |
| HTTP           | WP REST API                                | Namespace `one-point/v1`, controllers extend `WP_REST_Controller` |
| Data           | WordPress MySQL via `$wpdb`                | Custom table `wp_emp_transfers`, not posts/postmeta               |
| Async dispatch | WordPress Action Hooks (`do_action`)       | To IT, Facilities, Payroll coordinators                           |
| Tests          | PHPUnit + Brain Monkey / `WP_UnitTestCase` | See `.agent/rules/int-standards.wordpress.md`                     |
| Lint           | PHPCS, WPCS ruleset                        | Zero errors required                                              |

No plugin scaffold, no `wp_emp_transfers` schema, and no route registration exist in this
repository yet. Everything in the table above is a constitutional constraint on what will be
built, not a description of what exists.

## 2. Composition and request path

Not yet designed. Will be authored in the Plan stage for `emp-internal-transfer`, once a spec is
Approved at Gate 1.

## 3. Data layer

No schema exists yet. `wp_emp_transfers` and its `dbDelta()` migration will be designed and
documented here once the spec's Data model changes section is written.

## 4. Domain inventory

No code exists to inventory. Unlike the previous project's retro-spec back-fill (which inventoried
52 existing bounded contexts from a running codebase), this project has no legacy surface to
discover — the inventory step described in the SDD blueprint does not apply to a greenfield first
feature. This section will instead track feature slugs as they are specced.

## 5. Deployment

Not yet decided. Cloud/hosting provider, environments, and CI/CD are `[Open]` per
`constitution.md`.

## 6. Decisions in force

See `decisions/` — currently empty. No ADRs have been raised yet for this project.
