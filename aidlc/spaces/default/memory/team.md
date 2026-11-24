# Team-Level Rules

> This team's affirmed practices and corrections. Loaded after `org.md` as
> strict-additive guidance.
> Populated by the practices-discovery affirmation gate. Edit at the gate,
> not directly.

## Way of Working


- **Integration branch**: main
- Use short-lived branches and squash-merge each completed change into `main`.

## Walking Skeleton


- Build a thin end-to-end slice first, verify it, and obtain checkpoint approval before starting later units.

## Testing Posture


- **Methodology**: atdd
- **Ordering**: Agree on acceptance tests before implementation, then build to pass them.
- **Methodology evidence**: ATDD was selected because acceptance tests are agreed before implementation and guide development toward passing them.
- Require at least 80% line coverage, enforced in CI before merge, and keep the existing test suite green.

## Guard Policy

<!-- Affirmed by the team. Mode: strict, relaxed, or off. Strict here holds for every intent, whatever one person asks in chat; relaxed or off here applies to every intent whose policy came from its scope, unless project.md or the person sets another; changing this line changes it. A section under the retired Change Control heading, written by an earlier release, is still read. -->

## Deployment


- Deploy merged changes to staging automatically.
- Require manual approval from the tech lead before production deployment.

## Code Style


- Follow the repository's formatter and linter configuration; if none exists, use language defaults and idiomatic naming.
## Forbidden

<!-- Team-specific forbidden patterns -->

## Mandated

<!-- Team-specific mandates -->

## Corrections

<!-- Self-learning loop appends here. -->
