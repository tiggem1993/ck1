# Inventory Management API User Stories

Stories are grouped by the domain capabilities selected during elicitation. Every capability explicitly requested for the first release is prioritized **Must Have**; the MVP boundary remains for Delivery Planning. Acceptance criteria use Given/When/Then and preserve role-specific behavior within the combined Inventory Staff persona.

## 1. Catalog

### US1.1 — Maintain products and variants

**As a** Manager/Admin, **I want** to create, view, update, and retire products and variants, **so that** the API remains the authoritative product catalog.

- **Priority:** Must Have
- **Acceptance criteria:**
  - **AC1.1.1:** Given an authorized Manager/Admin creates a product with valid variants, when the product is read, then the product and each independently maintained variant are returned.
  - **AC1.1.2:** Given a product or variant is created, when it is persisted, then the API assigns a unique SKU to each variant and stores an optional manufacturer part number separately.
  - **AC1.1.3:** Given a Manager/Admin updates or retires a product or variant, when the change succeeds, then the catalog reflects it and previously recorded movement history remains available; an unauthorized staff update is denied.
  - **AC1.1.4:** Given a product request contains an invalid variant or a duplicate SKU, when creation is attempted, then it is rejected without creating a partial product or duplicate variant.
- **INVEST:** Valuable and testable catalog ownership; the individual product and variant operations are independently testable.

### US1.2 — View stock by location

**As an** Inventory Staff member, E-commerce Client, or POS Client, **I want** to view on-hand, reserved, and available-to-sell quantities for a SKU at a location, **so that** I can make an accurate inventory decision.

- **Priority:** Must Have
- **Acceptance criteria:**
  - **AC1.2.1:** Given a SKU has quantities at multiple locations, when one location is queried, then only that location's on-hand and reserved quantities are returned; a location without stock reports zero.
  - **AC1.2.2:** Given a saleable location has on-hand and reserved units, when availability is read, then available-to-sell is on-hand minus reserved.
  - **AC1.2.3:** Given units are in an RMA/defective quarantine location, when availability is read, then those units are excluded from available-to-sell.
  - **AC1.2.4:** Given either service client reads stock after a successful committed sale, when the next read completes, then it returns the updated quantity.
- **INVEST:** Valuable to staff and both integrations; independently testable per location and client.

## 2. Stock Movement

### US2.1 — Receive stock

**As an** Inventory Staff member, **I want** to record a stock receipt for a SKU and location, **so that** the available inventory reflects delivered goods.

- **Priority:** Must Have
- **Acceptance criteria:**
  - **AC2.1.1:** Given a valid SKU, location, quantity, and Purchase Receipt reason, when an authorized staff member records a receipt, then on-hand quantity increases and a movement is recorded.
  - **AC2.1.2:** Given the SKU or location is invalid, when a receipt is submitted, then it is rejected and neither the quantity nor movement history changes.
- **INVEST:** A focused, independently testable stock-in workflow.

### US2.2 — Adjust or write off stock

**As a** Manager/Admin, **I want** to make a manual stock adjustment or write-off with a reason, **so that** inventory records can be corrected or reduced under controlled access.

- **Priority:** Must Have
- **Acceptance criteria:**
  - **AC2.2.1:** Given a Manager/Admin submits a valid adjustment or write-off with a supported reason code, when it succeeds, then the on-hand quantity and movement history reflect the delta and reason.
  - **AC2.2.2:** Given a caller lacks the Manager/Admin role or supplies an invalid reason, when an adjustment is submitted, then it is denied and stock remains unchanged.
- **INVEST:** Valuable control operation with a clear authorization boundary and pass/fail outcomes.

### US2.3 — Transfer stock between locations

**As an** Inventory Staff member or Manager/Admin, **I want** to record a manual transfer between locations, **so that** the inventory record follows the physical stock.

- **Priority:** Must Have
- **Acceptance criteria:**
  - **AC2.3.1:** Given sufficient transferable stock exists, when an authorized staff member submits a transfer, then the source decreases and destination increases by the same quantity and both movements use the Location Transfer reason.
  - **AC2.3.2:** Given the source has insufficient available stock, when a transfer is submitted, then neither location quantity changes.
  - **AC2.3.3:** Given a manual transfer is valid, when it is submitted by either permitted staff role, then it completes without a separate approval; automated transfers are not part of this release.
- **INVEST:** A small, valuable workflow with independently testable atomic outcomes.

### US2.4 — Dispose of quarantined stock

**As an** Inventory Staff member or Manager/Admin, **I want** to record a disposition for quarantined stock, **so that** repaired, returned, or scrapped units are reconciled before the quarantine case closes.

- **Priority:** Must Have
- **Acceptance criteria:**
  - **AC2.4.1:** Given a quarantine case contains units, when its disposition is Repair, then the units move from quarantine to a saleable location and the movement is recorded with the Location Transfer reason.
  - **AC2.4.2:** Given a Manager/Admin records a Supplier Return or Scrap disposition, when it succeeds, then on-hand quantity is reduced and the disposition movement is recorded with the RMA/Defective reason.
  - **AC2.4.3:** Given no disposition has been recorded, when a caller attempts to close the quarantine case, then closure is rejected; after a valid disposition is recorded, closure can proceed.
  - **AC2.4.4:** Given quarantined units have not been repaired and moved to a saleable location, when availability is read or a sale is attempted, then those units remain unavailable.
  - **AC2.4.5:** Given an Inventory Staff member attempts a Supplier Return or Scrap disposition that reduces on-hand stock, when the request is evaluated, then it is denied and the quarantined quantity remains unchanged.
- **INVEST:** Captures distinct outcomes as independently testable state changes; disposition-specific authorization is governed by the existing staff operation permissions.

## 3. Reservations and Sales

### US3.1 — Reserve available stock

**As an** Inventory Staff member, E-commerce Client, or POS Client, **I want** to reserve available stock for an order or custom build, **so that** another operation cannot allocate the same units.

- **Priority:** Must Have
- **Acceptance criteria:**
  - **AC3.1.1:** Given sufficient saleable stock is available, when an authorized staff member or scoped service client reserves units, then reserved quantity increases and available-to-sell decreases by the same amount.
  - **AC3.1.2:** Given a reservation exceeds available stock, when it is submitted, then it is rejected without changing on-hand or reserved quantities.
  - **AC3.1.3:** Given concurrent reservations compete for the same final available units, when both are submitted, then no more units are reserved than were available.
- **INVEST:** Valuable for all ordering personas and testable for both normal and competing requests.

### US3.2 — Manage reservation lifecycle

**As an** Inventory Staff member or service client, **I want** reservations to follow the order or build lifecycle, **so that** stock is held only while it is needed.

- **Priority:** Must Have
- **Acceptance criteria:**
  - **AC3.2.1:** Given a custom-build reservation exists, when a Technician completes or cancels the build, then the reservation is finalized or released accordingly.
  - **AC3.2.2:** Given an online-checkout reservation is cancelled or reaches its configured expiry, when that event is processed, then reserved quantity is released.
  - **AC3.2.3:** Given payment succeeds for an online checkout, when the payment is finalized, then the reservation becomes a sale and the stock consumption is committed.
  - **AC3.2.4:** Given checkout expiry duration has not been benchmarked, when a reservation is created, then the configured duration is applied without asserting an unapproved numeric value.
- **INVEST:** Separates reservation lifecycle events into independently testable scenarios without locking an unbenchmarked duration.

### US3.3 — Record fulfillment

**As a** Technician, E-commerce Client, or POS Client, **I want** to record fulfillment for the reservation I am responsible for, **so that** the completed order or custom build is reflected in inventory.

- **Priority:** Must Have
- **Acceptance criteria:**
  - **AC3.3.1:** Given a valid custom-build reservation, when a Technician records fulfillment, then the fulfillment is associated with that reservation and its reserved quantity is consumed once.
  - **AC3.3.2:** Given a valid online-order reservation, when its E-commerce or POS service client records fulfillment, then the fulfillment is associated with the reservation and its reserved quantity is consumed once.
  - **AC3.3.3:** Given a reservation is unknown or fulfillment exceeds its reserved quantity, when fulfillment is submitted, then it is rejected without partial consumption.
- **INVEST:** Assigns each fulfillment to its selected actor and provides isolated success and failure cases.

### US3.4 — Commit sales before returning success

**As an** E-commerce Client or POS Client, **I want** each sale deduction to commit before the API reports success, **so that** checkout and POS results match inventory.

- **Priority:** Must Have
- **Acceptance criteria:**
  - **AC3.4.1:** Given a sale can be fulfilled, when either service client submits it, then the deduction commits before success is returned and is visible on the next stock read.
  - **AC3.4.2:** Given a sale cannot be committed because stock is unavailable or an operation fails, when the request completes, then the API does not return success and no partial quantity change remains.
- **INVEST:** A focused integration outcome with directly observable success and failure behavior.

### US3.5 — Protect against duplicate sale retries

**As an** E-commerce Client or POS Client, **I want** each sale to use a stable order reference, **so that** retrying a request cannot deduct stock twice.

- **Priority:** Must Have
- **Acceptance criteria:**
  - **AC3.5.1:** Given a new sale reference and sufficient stock, when a service client submits the sale, then one deduction is applied.
  - **AC3.5.2:** Given the same sale reference is submitted again, when the retry is processed, then no second deduction is applied.
- **INVEST:** Small, valuable idempotency behavior with clear retry assertions.

## 4. Access and Audit

### US4.1 — Enforce staff roles and service-client scopes

**As an** Inventory Staff member, Manager/Admin, E-commerce Client, or POS Client, **I want** each protected operation checked against my authenticated role or scope, **so that** only permitted inventory actions can change stock or catalog data.

- **Priority:** Must Have
- **Acceptance criteria:**
  - **AC4.1.1:** Given Inventory Staff or a Manager/Admin requests an operation allowed for its role, when the request is evaluated, then it succeeds; an operation outside that role is denied without a stock change.
  - **AC4.1.2:** Given an authenticated service client has a scope for stock reads, reservations, or sale deductions, when it makes a valid request within that scope, then it succeeds; an out-of-scope operation is denied without changing inventory.
  - **AC4.1.3:** Given a staff user or service client is unauthenticated, when it requests a protected operation, then the API denies the request.
- **INVEST:** Valuable security boundary with independently verifiable allowed and denied operations.

### US4.2 — Preserve immutable stock movement history

**As a** Manager/Admin, **I want** every stock change to have immutable, retained movement history, **so that** financial audits, warranty/RMA disputes, and shrinkage investigations can be supported.

- **Priority:** Must Have
- **Acceptance criteria:**
  - **AC4.2.1:** Given a stock-changing operation succeeds, when movement history is read, then each entry contains actor, timestamp, SKU, location, quantity delta, and reason code.
  - **AC4.2.2:** Given a movement entry has been recorded, when an API caller attempts to edit or delete it, then the API rejects the attempt.
  - **AC4.2.3:** Given a stock change is attempted, when it fails, then the operation reports failure and leaves neither a partial quantity change nor a partial audit record.
  - **AC4.2.4:** Given a movement record is created, when seven years have not elapsed, then it remains available in movement history.
- **INVEST:** Valuable audit outcome with observable fields, retention, immutability, and atomicity checks.

## 5. Integrations and File Exchange

### US5.1 — Preview and confirm supplier imports

**As a** Manager/Admin, **I want** to preview and confirm supplier catalog and invoice imports from CSV or Excel workbooks, **so that** supplier data can be loaded with row-level validation before it affects the system.

- **Priority:** Must Have
- **Acceptance criteria:**
  - **AC5.1.1:** Given a supported supplier file contains valid, invalid, or duplicate rows, when the Manager/Admin requests a preview, then row-level validation results identify errors and duplicates before confirmation.
  - **AC5.1.2:** Given an import preview is ready, when the Manager/Admin confirms it, then valid rows are imported while invalid or duplicate rows are reported and skipped.
  - **AC5.1.3:** Given the caller is not a Manager/Admin, when it attempts to preview or confirm an import, then the request is denied.
- **INVEST:** Valuable, independently testable import workflow; supplier-specific field mapping and duplicate keys remain deferred.

### US5.2 — Approve pending invoice receipts

**As a** Manager/Admin, **I want** to approve an imported invoice receipt, **so that** received quantities affect stock only after an authorized review.

- **Priority:** Must Have
- **Acceptance criteria:**
  - **AC5.2.1:** Given an invoice is imported but not approved, when stock is read, then its pending quantities are not included in on-hand totals.
  - **AC5.2.2:** Given a Manager/Admin approves the invoice receipt, when approval succeeds, then the quantities are recorded as received stock.
  - **AC5.2.3:** Given the caller lacks the Manager/Admin role, when it attempts to approve an invoice receipt, then approval is denied and on-hand stock remains unchanged.
- **INVEST:** A discrete approval decision with clear before-and-after stock outcomes.

### US5.3 — Export inventory for accounting and tax

**As a** Manager/Admin, **I want** to request an inventory CSV export, **so that** accounting and tax reconciliation can use inventory data.

- **Priority:** Must Have
- **Acceptance criteria:**
  - **AC5.3.1:** Given an authorized Manager/Admin requests an export, when it completes, then the API returns inventory data in CSV format.
  - **AC5.3.2:** Given the caller is not a Manager/Admin, when it requests an export, then the API denies the request.
- **INVEST:** Valuable reporting outcome with clear format and authorization tests; exact columns and filters remain open for contract design.

## Non-Functional Coverage and Dependencies

- **NFR1 — Audit retention:** Covered by AC4.2.4; movement history is retained for at least seven years.
- **NFR2 — Stock integrity:** Covered by the atomic failure criteria in AC2.2.2, AC2.3.2, AC3.1.2, AC3.3.3, AC3.4.2, and AC4.2.3.
- **NFR3 — Access control:** Covered by US4.1 and its role/scope acceptance criteria.
- **NFR4 — Catalog scale and service targets:** Deferred to `nfr-requirements` because numeric latency, availability, and peak request-rate targets require benchmarking; the initial catalog expectation remains 1,000–10,000 active SKUs.

Dependency and relationship notes:

- US1.1 establishes catalog records needed by receiving, availability, and downstream stock operations.
- US2.1 establishes on-hand stock used by US1.2, US3.1, and sale workflows.
- US3.1 reservations feed US3.2 and US3.3; sale finalization in US3.4 must be protected by US3.5.
- US4.1 authorization applies across all protected stories; US4.2 movement history accompanies every successful stock-changing story.
- US5.1 invoice imports create pending receipts used by US5.2; approval then follows the receipt behavior in US2.1.

## Open Decisions Retained from Requirements Analysis

- Numeric latency, availability, and peak request-rate targets, and the checkout reservation expiry duration, require benchmarking.
- Supplier-specific import field mappings and duplicate-matching keys are not yet defined.
- Behavior for reservations attached to retired products or variants remains undecided.
- Accounting/tax export columns and selection filters remain undecided.
