# Inventory Management REST API Requirements

## Intent Analysis

Build a REST API that gives internal staff and the e-commerce and POS systems a reliable, current view of component inventory. The API is the product-catalog source of truth and must support location-level stock, reservations, receipts, adjustments, fulfillment, integrations, and an auditable movement history. [desc] [Q1] [Q2] [Q11]

The first release serves one business, with an expected catalog of approximately 1,000–10,000 active component SKUs. Exact service-level and checkout-hold targets remain for later benchmarking. [Q7] [Q8] [Q19]

## Sources

- `[desc]` Initial description: Build a REST API for inventory management.
- `[scope]` Workflow-selected scope: classic.
- `[Q1]`–`[Q21]` Answers in [requirements-analysis-questions.md](./requirements-analysis-questions.md).
- Team delivery practices: [team-practices.md](../practices-discovery/team-practices.md).

## Functional Requirements

### Product catalog and stock availability

- **FR1 — Product and variant management.** The API shall let authorized users create, view, update, and retire products and their variants. Retirement shall preserve existing movement history. [Q2] [Q4]
  - **Acceptance:** Given an authorized manager creates a product with multiple variants, when the product is read, then each variant is present and can be maintained or retired independently; given an invalid variant, when creation is attempted, then it is rejected without leaving a partial product.
- **FR2 — SKU and manufacturer identifiers.** The API shall assign a unique SKU to each variant and store its manufacturer part number separately when one is available. [Q4] [Q11]
  - **Acceptance:** Given two variants of the same product, when they are created, then each has a different API-assigned SKU and its own optional manufacturer part number; an SKU collision shall not create a second variant with the same SKU.
- **FR3 — Location-level quantities.** The API shall track on-hand and reserved quantities for each SKU at each location, including warehouse/backroom, storefront, and RMA/defective quarantine locations. [Q2] [Q5]
  - **Acceptance:** Given one SKU has stock at two locations, when either location is queried, then its quantities are returned separately rather than combined; a location with no stock shall report zero rather than another location's quantity.
- **FR4 — Available-to-sell quantity.** The API shall report on-hand and reserved values separately and calculate available-to-sell stock from unreserved stock at saleable locations. Quarantine stock shall not be available to sell. [Q3] [Q17]
  - **Acceptance:** Given a saleable location has on-hand and reserved units, when availability is read, then available-to-sell equals on-hand minus reserved; units in quarantine are excluded even when physically on hand.
- **FR5 — Preventing over-allocation.** The API shall reject a sale or reservation that exceeds available stock and leave the affected quantities unchanged. Concurrent operations shall not allocate the same units more than once. [Q3] [Q13]
  - **Acceptance:** Given sufficient units are available, when a sale or reservation is submitted, then it succeeds; given fewer units are available than requested, then it fails without changing stock or reservations.

### Receipts, adjustments, and transfers

- **FR6 — Stock receipts.** The API shall record received stock against a SKU and location as a stock movement. Sales/Technicians may receive stock. [Q1] [Q7]
  - **Acceptance:** Given an authorized staff member records a receipt for a valid SKU and location, when it succeeds, then on-hand and movement history reflect the received quantity; an invalid SKU or location is rejected without a quantity change.
- **FR7 — Manual adjustments and write-offs.** The API shall allow Managers/Admins to adjust or write off stock and require a reason code. The listed reason codes shall include `Sale`, `Purchase Receipt`, `Custom Build Allocation`, `RMA/Defective`, and `Cycle Count Adjustment`. [Q7] [Q9]
  - **Acceptance:** Given a Manager/Admin submits an adjustment with a valid reason, when it succeeds, then the quantity change and reason are recorded; a caller without that role or with an invalid reason is denied and stock remains unchanged.
- **FR8 — Manual location transfers.** Sales/Technicians and Managers/Admins may record manual transfers without separate approval. A transfer shall atomically reduce the source location and increase the destination by the same quantity, and record both movements with the `Location Transfer` reason code. Automated transfers are excluded from the first release. [Q5] [Q10] [Q20] [Q21]
  - **Acceptance:** Given an authorized staff member transfers available stock, when the transfer succeeds, then the source and destination quantities change by equal and opposite amounts and both movements are auditable; an insufficient-source transfer changes neither location.
- **FR9 — Quarantine disposition.** The API shall exclude RMA/defective quarantine stock from availability and require a recorded disposition, such as repair, supplier return, or scrap, before the quarantine case is closed. [Q5] [Q17]
  - **Acceptance:** Given stock is in quarantine, when a caller checks availability or attempts to sell it, then it is unavailable; after a disposition is recorded, the quarantine can be closed, while closure without a disposition is rejected.

### Reservations, sales, and fulfillment

- **FR10 — Order reservations.** The API shall reserve stock for online checkouts and custom PC builds, keeping reserved quantities distinct from on-hand quantities. [Q2] [Q3]
  - **Acceptance:** Given sufficient available stock, when an authorized staff member or service client reserves units, then reserved quantity increases and available-to-sell quantity decreases by the same amount; a request exceeding availability is rejected without changing either value.
- **FR11 — Reservation lifecycle.** A custom-build reservation shall remain until a technician completes or cancels the build. An online-checkout reservation shall release on cancellation or expiry; payment shall finalize it as a sale. The checkout expiry duration is intentionally undecided pending benchmarking. [Q12] [Q16] [Q19]
  - **Acceptance:** Given a custom-build reservation, when the build is completed or cancelled, then the reservation is finalized or released; given an unpaid checkout reservation, when it is cancelled or its configured expiry occurs, then its reserved quantity is released; payment finalizes the checkout reservation as a sale.
- **FR12 — Fulfillment recording.** The API shall record fulfillment for a reserved order or custom build and apply the corresponding stock consumption no more than once. [Q2] [Q16]
  - **Acceptance:** Given a valid reservation, when fulfillment is recorded, then the fulfillment is associated with that reservation and the consumed quantity is reflected in stock and movement history; an unknown reservation or fulfillment exceeding its reserved quantity is rejected without a partial consumption.
- **FR13 — Sale consistency.** The API shall validate availability and commit each sale deduction before returning success; the updated quantity shall be visible to the next read. [Q1] [Q6] [Q13]
  - **Acceptance:** Given a sale can be fulfilled, when the API returns success, then a subsequent stock read reflects the deduction; if the deduction cannot be committed, the API does not return success.
- **FR14 — Duplicate sale protection.** The API shall require a stable sale or order reference and apply a given sale deduction no more than once when a caller retries the same request. [Q18]
  - **Acceptance:** Given a new sale reference and sufficient stock, when the first request is submitted, then the sale succeeds; when the same reference is retried, then no second deduction is applied.

### Access, audit, and integrations

- **FR15 — Staff role permissions.** Staff access shall be role-based: Sales/Technicians may view stock, reserve stock, receive stock, and record manual transfers; Managers/Admins may create or retire SKUs and perform manual adjustments/write-offs, and may record manual transfers. [Q7] [Q10] [Q21]
  - **Acceptance:** Given a staff member performs an operation allowed for their role, when the request is evaluated, then it succeeds; an operation outside that role is denied and stock remains unchanged.
- **FR16 — Scoped service clients.** The API shall authenticate scoped service clients for the e-commerce storefront and POS. Their permitted operations shall include reading stock, creating reservations, and recording sale deductions. [Q1] [Q6] [Q7] [Q13]
  - **Acceptance:** Given an authenticated service client calls an operation within its granted scope, when the request is valid, then it succeeds; an operation outside its scope is denied without changing inventory.
- **FR17 — Immutable movement history.** Every stock change shall create an immutable audit entry recording the actor (staff user ID or system client), timestamp, SKU, location, quantity delta, and reason code. Entries shall be readable as movement history and shall not be editable or deletable through the API. [Q2] [Q9] [Q20]
  - **Acceptance:** Given any stock-changing operation succeeds, when its movement history is read, then the actor, time, SKU, location, delta, and reason are present; attempts to edit or delete an entry through the API are rejected.
- **FR18 — E-commerce and POS inventory use.** The e-commerce storefront and POS shall be able to read current inventory and submit order or sale deductions through authenticated service access. [Q1] [Q6] [Q7] [Q13]
  - **Acceptance:** Given a sale is accepted from either system, when that system reads the affected SKU afterward, then it sees the committed stock level; an unavailable-stock sale from either system is rejected without a deduction.

### File exchange

- **FR19 — Supplier catalog and invoice imports.** The API shall accept CSV and Excel workbooks for supplier catalog and invoice imports, present a validation preview, and require confirmation before importing. Invalid or duplicate rows shall receive row-level errors. [Q6] [Q14] [Q15]
  - **Acceptance:** Given an uploaded file contains valid and invalid rows, when it is validated, then the preview identifies row errors before import confirmation; after confirmation, valid rows are imported while invalid or duplicate rows are reported and skipped.
- **FR20 — Invoice receipt approval.** An imported invoice shall create a pending stock receipt; its quantities shall not affect on-hand stock until a Manager/Admin approves it. [Q14]
  - **Acceptance:** Given an invoice has been imported but not approved, when stock is read, then pending quantities are not included; after Manager/Admin approval they are reflected, while an unauthorized approval leaves stock unchanged.
- **FR21 — Accounting and tax export.** The API shall export inventory data as CSV for accounting and tax reconciliation. [Q6]
  - **Acceptance:** Given an authorized export request, when the export completes, then it returns a CSV file containing the requested inventory data; an unauthorized export request is denied.

## Non-Functional Requirements

- **NFR1 — Audit retention.** Retain immutable stock-movement history for at least seven years. [Q9]
- **NFR2 — Stock integrity.** Stock changes, reservations, transfer legs, and their audit records shall be applied consistently; a failed operation shall not report success or leave a partial quantity change. [Q3] [Q9] [Q10] [Q13]
- **NFR3 — Access control.** The API shall authenticate staff and service clients and enforce their role or scope on the server for every protected operation. [Q7]
- **NFR4 — Catalog scale and service targets.** The expected initial catalog is 1,000–10,000 active component SKUs. No numeric latency, availability, or peak request-rate target is approved; these remain open for benchmarking against actual checkout traffic. [Q8]

## Constraints

- The API owns the product catalog and assigns SKUs; manufacturer part numbers are separate identifiers when available. [Q11]
- The first release supports multiple locations and staff-recorded transfers, but no automated inter-location transfers. [Q5] [Q10]
- One business is in scope; multi-tenant isolation is not required in this release. [Q7]
- Supplier imports use CSV and Excel workbooks with preview and confirmation; accounting/tax export uses CSV. [Q6] [Q15]
- The team follows ATDD, requires at least 80% line coverage enforced in CI before merge, and keeps the existing test suite green. See [team-practices.md](../practices-discovery/team-practices.md).

## Assumptions

- Retiring a product or variant marks it inactive rather than deleting its historical movements; the detailed treatment of existing reservations is for a later design decision.
- Import validation is row-oriented: after confirmation, valid rows may be applied while invalid or duplicate rows are reported and skipped. The exact duplicate-matching key and supplier-specific field mapping will be defined with the import contract.
- A transfer is represented by paired source and destination movements with the same transfer identity and the `Location Transfer` reason code.

## Out of Scope

- Automated transfers between locations in the first release. [Q5]
- Multi-tenant inventory separation. [Q7]
- Setting numeric SLA, availability, latency, or peak request-rate commitments before benchmarking. [Q8]

## Open Questions

- What online-checkout reservation expiry duration best fits measured checkout traffic? [Q19]
- What latency, availability, and peak request-rate targets are appropriate for the expected catalog and checkout load? [Q8]
- Which supplier-specific field mappings and duplicate-matching keys are required for catalog and invoice imports? [Q15]
- What should happen to existing reservations when a product or variant is retired?
- Which CSV columns and selection filters are required for accounting and tax exports? [Q6]
