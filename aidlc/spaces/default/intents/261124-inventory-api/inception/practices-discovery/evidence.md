# Known Evidence

## Project Context

- Initial project description: "Build a REST API for inventory management".
- The intent is Greenfield and uses the `classic` scope.

## Defaults and Affirmed Decisions

- The org defaults suggested `main` as the integration branch, short-lived work with squash merges, test-after ordering with no additional coverage floor for `classic` (while keeping the existing suite green), automatic staging deployment on merge with separate manual production approval, and repository-configured style rules with language defaults and idiomatic naming when no configuration exists. These were framework defaults, not established project decisions.
- The interview affirmed `main` as the integration branch and short-lived branches with squash-merge for each completed change.
- The interview affirmed building and verifying a thin end-to-end slice first, with checkpoint approval required before later units.
- The interview selected ATDD: agree on acceptance tests before implementation, then build to pass them. It also set an 80% minimum line-coverage floor enforced in CI before merge and affirmed keeping the existing suite green.
- The interview affirmed automatic deployment of merged changes to staging and manual production approval by the tech lead.
- The interview affirmed following the repository formatter and linter configuration, using language defaults and idiomatic naming if none exists.
- The interview specified no additional hard constraints beyond the selected practices. Accordingly, `discovered-rules.md` retains `None.` under both `## Mandated` and `## Forbidden`.

## Interview Coverage and Remaining Context

- Answered areas: integration branch; branching and merge approach; first thin end-to-end slice and checkpoint; testing method and ordering; coverage and CI; staging and production approval; code style; additional hard constraints.
- No application language, framework, or build system has been established in the available evidence. The repository's formatter and linter configuration also remains unknown.
- No specialist reviews were requested or enabled, and no contribution files were produced.
