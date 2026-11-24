<!-- INVARIANT: examples are single-line HTML comments so a fresh template parses to total=0 (MEMORY_EMPTY). Do NOT un-comment or split across lines. t100 guards this. -->
> This file is kept up to date automatically while the stage runs.

## Interpretations
<!-- example: 2026-05-29T10:14:32Z — chose REST over GraphQL; the consuming team only needs CRUD, revisit if subscriptions land -->
- 2026-11-24T18:41:36Z — treated “Build a REST API for inventory management” as a goal, not a feature specification; the users, inventory model, stock rules, integrations, access control, and service targets need human input.
- 2026-11-24T19:22:09Z — treat each product variant as a separately tracked SKU, with the API authoritative for product data and manufacturer part numbers stored separately. Exclude RMA/defective quarantine from available-to-sell stock; require a recorded disposition before it leaves quarantine. Custom-build holds remain until completion or cancellation; paid checkout finalizes the sale, and a stable sale/order reference prevents duplicate deductions.
- 2026-11-24T19:23:54Z — record manual transfers as paired source and destination movements with a dedicated `Location Transfer` reason code, so both sides are identifiable in the immutable history.
- 2026-11-24T19:26:27Z — Sales/Technicians and Managers/Admins may record manual transfers without separate approval.

## Deviations
<!-- example: 2026-05-29T10:14:32Z — skipped the optional caching layer the stage prose suggested; the dataset is small enough that it adds risk -->

## Tradeoffs
<!-- example: 2026-05-29T10:14:32Z — picked TDD over BDD this run; the team is unit-first and the domain is well-understood -->
- 2026-11-24T19:22:09Z — keep stock deductions synchronous and visible on the next read, while allowing staff to record manual location transfers without automated transfers. Supplier catalog and invoice imports require validation preview and confirmation; imported invoices create pending receipts until manager approval.

## Open questions
<!-- example: 2026-05-29T10:14:32Z — confirm the retention window with compliance before the next stage hardens the schema -->
- 2026-11-24T19:01:50Z — the first answers leave several behavior boundaries unresolved: whether staff can record manual location transfers, catalog ownership and identifier mapping, reservation expiry, sale-update consistency, and invoice-import effects.
- 2026-11-24T19:22:09Z — the exact online-checkout reservation duration, SLA percentages, and peak request rate remain open for benchmarking against checkout traffic and the expected 1,000–10,000 active SKUs.
