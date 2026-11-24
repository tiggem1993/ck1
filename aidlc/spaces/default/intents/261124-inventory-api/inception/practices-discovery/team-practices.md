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

## Deployment

- Deploy merged changes to staging automatically.
- Require manual approval from the tech lead before production deployment.

## Code Style

- Follow the repository's formatter and linter configuration; if none exists, use language defaults and idiomatic naming.
