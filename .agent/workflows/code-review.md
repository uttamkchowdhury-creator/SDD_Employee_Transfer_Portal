# Workflow: /code-review (Gate 2)

Gate 2 reviews generated code against the spec that produced it. It does not re-litigate the
spec — that was Gate 1's job. "The agent wrote it" is never a valid answer to a review comment.

## Prompt template

```
Review the diff for <slug>.<task-id>.

Read:
- .ai-context/specs/<slug>.spec.md          (verify each AC by ID)
- .ai-context/plans/<slug>.plan.md          (check nothing deferred was built)
- .ai-context/constitution.md               (security posture, architectural constraints)
- .agent/rules/int-standards.wordpress.md

Report findings grouped as: Blocking / Should-fix / Nit.
For each finding cite the rule or AC ID it violates. Do not restate what the code does.
```

## Gate 2 checklist

- [ ] Each acceptance criterion verified individually **by ID** against the diff — not "looks reasonable"
- [ ] Tests were written first and confirmed Red before Green, not retrofitted
- [ ] Nothing the plan explicitly deferred has been built (scope creep check)
- [ ] No AI attribution in comments or commit messages (task-ID references are fine)
- [ ] `architecture.md` / ADR updated if the change warrants it
- [ ] `.ai-context/test_cases/<slug>.test_cases.md` and spec Status updated
- [ ] `.ai-context/status.md` updated the same day
- [ ] `phpcs` (WPCS ruleset, zero errors) and the PHPUnit suite both clean
- [ ] Standard review still applies: readability, naming, dead code, duplication

## Security checklist (also run on every hotfix)

- [ ] No PII in logs at any level — employee name, ID, contact detail, department/location, and
      manager identity never reach `error_log()`, `WP_DEBUG_LOG`, or any log sink in plaintext
- [ ] No secrets, credentials, or tokens hardcoded or logged
- [ ] Every route's `permission_callback` uses `current_user_can()` (or a mapped capability) —
      never `__return_true` for a state-changing or PII-bearing route
- [ ] Nonce verification present on every authenticated write path
- [ ] Every input sanitized at the boundary (`sanitize_text_field()`, `sanitize_email()`,
      `absint()`, etc.) and every output escaped (`esc_html()`, `esc_attr()`, `esc_url()`)
- [ ] Every `$wpdb` query touching external input uses `$wpdb->prepare()` — no exceptions
- [ ] Every new or changed REST route has an explicit rate-limit decision
- [ ] Auth boundary and capability gate checked, not assumed
- [ ] New dependencies (Composer packages, must-use plugins) vetted before entering the manifest
- [ ] `$wpdb` access confined to repository classes; no raw query in a REST controller
- [ ] Hot-path queries checked for missing indexes on the custom table

## Architecture-specific traps in this project

- A REST controller that runs `$wpdb` queries directly instead of delegating to a repository
  class is a layering violation, not a style nit.
- A `permission_callback` check without a paired nonce check is a CSRF gap even if the capability
  check itself is correct.
- `do_action()` dispatch to a downstream coordinator (IT/Facilities/Payroll) with no registered
  listener fails silently — verify a listener actually exists before treating a hook call as done.
