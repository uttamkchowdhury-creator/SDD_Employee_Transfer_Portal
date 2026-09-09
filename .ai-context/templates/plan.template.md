# Plan: <Feature Name>

## Derived From
.ai-context/specs/<feature-slug>.spec.md (Status must be **Approved** before this plan is written)

## Architecture Approach
<Components touched, new vs. existing, integration points. Name them explicitly — do not leave
anything for the agent to infer at implementation time.>

- Plugin: `wp-content/plugins/<slug>/`
- REST controller(s): `includes/Rest/<Feature>Controller.php`
- Service class(es): `includes/Service/<Feature>Service.php`
- Repository class(es): `includes/Repository/<Feature>Repository.php`
- Hooks fired/consumed: `do_action('<hook_name>', ...)` — list each, direction, and listener

## Data Model
<Schema changes and migrations, if any. State "no schema change" explicitly when that is the case.>

- Table(s) added/changed: `wp_<table>`, or none
- `dbDelta()` migration required: <yes/no>
- Plugin `db_version` bump: <yes/no>

## Constitution Check
<!-- A real verdict per line. Silence on a rule is a gap, not a neutral omission. -->
- [ ] No new datastore, queue, or scheduling mechanism introduced without an ADR
- [ ] Layering respected — `$wpdb` only in repository classes, no business rules in controllers
- [ ] Testing discipline matches constitution.md (PHPUnit, test-first, 80% coverage floor)
- [ ] Security posture matches constitution.md (capability checks, nonce verification,
      sanitization/escaping, prepared SQL, no PII in logs)
- [ ] Rate-limit decision made explicit for every new or changed route
- [ ] Downstream dispatch (IT/Facilities/Payroll) goes through `do_action()`, not a new mechanism
- [ ] Non-functional baselines respected (p95 < 400 ms)

## Explicitly Deferred
- <item, with the reason it is deferred and where it is tracked>

<!-- Gate 2 verifies that implementation did not scope-creep into what was deferred here. -->

## ADR Impact
<Any decision that would cost more than a day of rework to reverse needs an ADR in
.ai-context/decisions/, not just a line here. State "none" if none.>

## Sequencing
1. <high-level build order — each item must be independently verifiable and mergeable>
2. ...
