# Workflow: /generate-tests

Tests are generated from the spec's own test-case table **before** any implementation task runs,
and must be confirmed failing (Red) before implementation begins. Retrofitting tests after the
code exists inverts the check that catches wrong-behaviour-by-design.

## Prompt template

```
Generate tests for <slug>.<task-id>.

Read:
- .ai-context/specs/<slug>.spec.md — the Unit Test Cases table and the Acceptance Criteria
- .ai-context/test_cases/<slug>.test_cases.md — QA-expanded scenarios, if present
- .agent/rules/int-standards.wordpress.md — test conventions

Write PHPUnit tests (Brain Monkey for unit-level WordPress function mocking, WP_UnitTestCase for
integration tests) under tests/, mirroring the plugin's class structure. One test per
<slug>.UT## row, named so the ID is greppable.

Cover, at minimum:
- Every acceptance criterion referenced by this task, by ID
- Every row of the REST route's exception table (not just the happy path plus one error)
- Auth boundary: unauthenticated, wrong capability, correct capability
- Nonce-missing / nonce-invalid rejection where the route requires one
- Validation failures for each required field

Do not write implementation code. Do not modify existing passing tests.
```

## Rules

- Test IDs from the spec appear in test names so traceability survives refactors.
- Derive cases from acceptance criteria, never from reading finished code.
- No snapshot-only assertions for logic-bearing behaviour.
- Never put real employee data, secrets, or live tokens in a fixture.

## After generation

1. Run the PHPUnit suite and **confirm the new tests fail** for the right reason. A test that
   passes before implementation is testing nothing.
2. Record the Red confirmation in the task entry — Gate 2 checks that tests were Red first.
3. Only then start the implementation task.
4. Generated tests are a starting point QA validates, not a replacement for QA judgement.
