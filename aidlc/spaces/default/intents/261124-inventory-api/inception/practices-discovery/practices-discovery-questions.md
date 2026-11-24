# Practices Discovery Questions

## Q1. [delivery lead] Integration branch
Which single branch should all team work merge into? The current proposal is `main`; this name will be recorded as the team's integration branch.

A. `main`
B. `develop`
C. Another branch (give its exact name)
X. Other (please specify)

[Answer]: A. `main`

## Q2. [delivery lead] Branching and merging
How should changes be developed and merged into that integration branch?

A. Use short-lived branches and squash-merge each completed change.
B. Use short-lived branches and create merge commits.
C. Commit directly to the integration branch.
D. Use a release-branch workflow with separate development and release branches.
E. Use another approach (describe it).
X. Other (please specify)

[Answer]: A. Use short-lived branches and squash-merge each completed change.

## Q3. [architect] Thin end-to-end slice
Build a thin end-to-end slice first? A walking skeleton is a minimal version that runs the whole way through, built first to prove the pieces connect before the real features go in.

A. Yes — build and verify that slice first, then approve a checkpoint before later units.
B. No — proceed with the planned work without a dedicated first-slice checkpoint.
X. Other (please specify)

[Answer]: A. Yes — build and verify that slice first, then approve a checkpoint before later units.

## Q4. [quality engineer] Testing method and order
Which approach should guide how tests and implementation are ordered?

A. Test-driven development (TDD): write a failing test, implement until it passes, then refactor.
B. Behavior-driven development (BDD): agree on behavior scenarios first, then implement and test the layers.
C. Acceptance-test-driven development (ATDD): agree on acceptance tests before implementation, then build to pass them.
D. Test-after: implement each applicable layer, then write and run that layer's tests.
E. Use a custom mix of methods or ordering (describe it).
X. Other (please specify)

[Answer]: C. Acceptance-test-driven development (ATDD): agree on acceptance tests before implementation, then build to pass them.

## Q5. [quality engineer] Coverage and CI
What additional test-coverage and CI rule should apply beyond keeping the existing suite green? The classic plan has no extra coverage floor by default.

A. Keep the existing-suite-green requirement; add no coverage threshold.
B. Set a line-coverage floor (give the percentage) and enforce it in CI before merge.
C. Set coverage targets for specified critical paths instead (name the paths and targets).
D. Use another coverage or CI rule (describe it).
X. Other (please specify)

[Answer]: B. Set a line-coverage floor of 80% and enforce it in CI before merge.

## Q6. [release engineer] Deployment and production approval
Which release approach should the team follow?

A. Deploy merged changes to staging automatically; require manual approval before production.
B. Require a person to approve each staging deployment and production deployment.
C. Deploy to production automatically when the required checks pass.
D. Use another approach (include who approves production and when).
X. Other (please specify)

[Answer]: A. Deploy merged changes to staging automatically; require manual approval before production by the tech lead.

## Q7. [developer] Code style
What should govern formatting, linting, naming, and other project-wide style rules?

A. Follow the repository's formatter and linter configuration; where none exists, use the language defaults and idiomatic naming.
B. Require specific formatter and linter tools (name them) and use idiomatic naming.
C. Define additional project-wide conventions (list them).
D. Use another approach (describe it).
X. Other (please specify)

[Answer]: A. Follow the repository's formatter and linter configuration; where none exists, use the language defaults and idiomatic naming.

## Q8. [delivery lead] Hard constraints
Are there any non-negotiable practices the team must always follow or must never use? These become explicit project rules; do not list preferences as hard constraints.

A. None beyond the practices selected above.
B. Yes — list each required or forbidden behavior.
X. Other (please specify)

[Answer]: A. None beyond the practices selected above.
