# INT Standards — WordPress / PHP (One-Point Employee Portal)

Always-on rules for this repository. The agent reads this every session, regardless of task.
Feature-specific rules belong in that feature's spec, not here.

Stack in force: WordPress (latest stable core, custom plugin architecture), PHP 8.x with strict
typing (`declare(strict_types=1);`), WP REST API, custom tables via `$wpdb` (no CPT/postmeta
modelling of transactional data), PHPUnit + Brain Monkey / `WP_UnitTestCase`, WPCS
(WordPress-Extra ruleset) enforced via PHPCS.

## Plugin structure

- All feature code lives under `/wp-content/plugins/emp-internal-transfer/` (or the equivalent
  plugin slug for a given feature) — never in the parent theme, never loose in `mu-plugins/`
  without an ADR.
- One bootstrap file per plugin (`<slug>.php`) that only registers hooks and autoloading —
  no business logic in the bootstrap.
- REST controllers, services, and repository/data-access classes are separated by directory
  (`includes/Rest/`, `includes/Service/`, `includes/Repository/`). A controller must not run raw
  `$wpdb` queries directly — that belongs in a repository class.

## Language conventions

- **PHP 8.x strict typing everywhere.** Every file starts with `declare(strict_types=1);`.
  Typed properties, typed method signatures, and return types are mandatory — no untyped `mixed`
  as a shortcut.
- Follow WordPress Coding Standards (WPCS) — brace style, spacing, Yoda conditions where WPCS
  requires them, naming (`snake_case` for functions/hooks, `Prefixed_Pascal_Case` for classes).
  `phpcs` with the WPCS ruleset must pass with zero errors before a task is done.
- Namespace all plugin code under a vendor-style prefix (e.g. `OnePoint\EmpInternalTransfer\`) to
  avoid global function/class collisions with core, other plugins, and the theme.
- No procedural spaghetti in REST callbacks — a route's `permission_callback` and request handler
  are thin, delegating to a service class.

## WP REST API conventions

- Every REST controller **extends `WP_REST_Controller`** and implements `register_routes()`,
  `get_item_schema()`, and explicit `args` validation/sanitization per parameter — do not accept
  an unvalidated `$request->get_params()` payload into a service method.
- Every route registers a `permission_callback` that is never `__return_true` for a
  state-changing or PII-bearing endpoint. `current_user_can()` (or a custom capability mapped
  through `map_meta_cap`) is mandatory on every route that reads or writes plugin data.
- Namespace is `one-point/v1` (or the feature's assigned namespace) — versioned, never
  unversioned. A breaking change to a released route requires a new version segment, not a
  silent contract change.
- Nonce verification (`wp_verify_nonce` / `X-WP-Nonce` via `rest_cookie_check_errors`) is
  required for any route reachable from an authenticated browser session, in addition to the
  capability check — a capability check is not a substitute for CSRF protection.

## Data layer

- WordPress MySQL via `$wpdb` is the system of record for this plugin's custom tables
  (e.g. `wp_emp_transfers`). No second datastore without an ADR.
- **Every SQL query touching user input goes through `$wpdb->prepare()`.** A raw string-concatenated
  query with any external input is a defect, not a style nit — no exceptions for "trusted" admin
  input.
- Schema changes ship via a versioned `dbDelta()` migration keyed to a plugin `db_version` option,
  checked and applied on `admin_init` / activation — never a hand-run `ALTER TABLE` against a
  shared environment.
- Custom table access is isolated to repository classes; services and controllers never import
  `$wpdb` directly.

## Security

- **Zero PII in error or debug logs, at any log level.** Employee names, emails, employee IDs,
  department/location detail, and manager identities never reach `error_log()`, `WP_DEBUG_LOG`
  output, or any third-party logging sink in plaintext — mask or omit before logging.
- **Strict capability checks** (`current_user_can()`) gate every state-changing action and every
  read of another employee's data. Do not rely on nonce presence alone, and do not roll a custom
  role check that bypasses WordPress's capability system.
- **Nonce verification** on every form submission and every authenticated REST write.
- **Input sanitization**: `sanitize_text_field()`, `sanitize_email()`, `absint()`, etc. applied at
  the boundary before data reaches a service or repository — never sanitize only on output and
  trust the input path.
- **Output escaping**: `esc_html()`, `esc_attr()`, `esc_url()` on every value rendered into HTML,
  admin screens, or emails — no unescaped interpolation of user- or DB-sourced strings.
- **Prepared SQL only** — see Data layer above. `$wpdb->prepare()` is not optional for any
  interpolated value.
- Secrets (API keys for downstream IT/Facilities/Payroll integrations, SMTP credentials) load from
  `wp-config.php` constants or environment variables — never hardcoded, never committed, never
  logged.

## Testing discipline

- **Test-first.** PHPUnit + Brain Monkey (for unit-level WP function mocking) or
  `WP_UnitTestCase` (for integration tests against a real WP bootstrap) — the test exists, is
  reviewed, and is confirmed failing (Red) before implementation (Green).
- Every custom REST endpoint and every hook-based side effect (`do_action` dispatch to IT,
  Facilities, Payroll) has test coverage for the happy path, the permission-denied path, and at
  least one validation-failure path.
- Coverage floor: **80% line coverage minimum**, measured on changed files, before a task is
  considered done.
- `phpcs` (zero errors, WPCS ruleset) and the PHPUnit suite must both pass before a task is done.

## Guardrails

- Do not introduce a second ORM, query builder, or database abstraction alongside `$wpdb`.
- Asynchronous downstream dispatch (IT, Facilities, Payroll provisioning) goes through WordPress
  Action Hooks (`do_action`) — do not add a queue, message broker, or cron-adjacent mechanism
  without an ADR.
- Do not add a new plugin dependency (Composer package or must-use plugin) without vetting it
  first; prefer WordPress core APIs over a third-party library where core already covers the need.
- No AI attribution in code comments or commit messages. A task-ID reference
  (`Implements <slug>.T03`) is traceability and is encouraged.
