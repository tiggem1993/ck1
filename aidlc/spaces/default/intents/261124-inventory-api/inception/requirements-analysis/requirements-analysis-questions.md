# Requirements Analysis Questions

## Q1. [product manager] Primary users and outcome
Who is the primary user or system that will call this inventory API, and what important task or business outcome should it support first?

A. Internal staff managing inventory for one business (name the roles and their main task).
B. Internal business systems that need current inventory data (name the systems and purpose).
C. External customers or business partners integrating with inventory (name the audience and purpose).
D. A combination of these groups (name each group and its first-use task).
E. Another primary user or outcome (describe it).
X. Other (please specify)

[Answer]: D. 4 - A combination of internal staff and internal business systems:

- **Internal Staff (Store/Warehouse Technicians & Sales Associates):** Need to look up real-time stock availability on the sales floor, receive incoming distributor shipments, and adjust stock for damaged/RMA parts.
- **Internal Business Systems (E-commerce Website & In-Store POS):** Need to read live inventory counts so customers cannot order out-of-stock components, and deduct stock immediately upon sale.

## Q2. [product manager] First-release capabilities
Which level best describes what the first release must let callers do?

A. Create, view, update, and retire inventory items, and read or update their current quantities.
B. A, plus record stock receipts and adjustments with a movement history.
C. B, plus track stock at multiple locations and transfer it between locations.
D. C, plus reserve or allocate stock for orders and record fulfillment.
E. A different set of included or excluded capabilities (list them).
X. Other (please specify)

[Answer]: D. 4 - Create, view, update, and retire inventory items; record stock receipts and adjustments with a movement history; track stock across locations (if applicable); and **reserve/allocate stock for customer orders (including custom PC builds)** and record fulfillment.

## Q3. [product manager] Handling stock shortages
When a requested operation would use more stock than is available, what should the API do?

A. Reject the operation and leave stock unchanged.
B. Allow the shortage as a backorder and show the resulting shortfall.
C. Allow it only through an explicit reservation or authorized override.
D. Track on-hand and reserved quantities separately, and prevent over-allocation.
E. Use another rule (describe it).
X. Other (please specify)

[Answer]: D. 4 - Track **on-hand** and **reserved** quantities separately, and prevent over-allocation. *(Note: Computer parts are frequently reserved for custom PC builds or pending online checkouts; separating on-hand vs. reserved ensures a part sitting on a technician's workbench isn't accidentally sold online).*

## Q4. [architect] Inventory item identity and counting
What is the countable inventory item, and how should it be uniquely identified?

A. Each item has one unique SKU; variants are not separate inventory items.
B. A product can have variants, and each variant has its own unique SKU.
C. Items have multiple units or pack sizes with defined conversion rules (describe them).
D. An existing product catalog defines the item identity (name the catalog or system).
E. Use another model (describe it).
X. Other (please specify)

[Answer]: B. 2 - A product can have variants, and each variant has its own unique SKU (e.g., an SSD model with 500GB, 1TB, and 2TB capacities, or DDR5 RAM with 16GB vs. 32GB kits, each tracked by its distinct Manufacturer Part Number / SKU).

## Q5. [architect] Locations and stock movement
Where must stock be tracked, and which movement between locations is required?

A. One store or warehouse; no transfers between locations.
B. Multiple locations, each with its own quantity; no transfers in the first release.
C. Multiple locations with transfers between them.
D. A location hierarchy or another location model (describe it).
X. Other (please specify)

[Answer]: B. 2. Multiple locations, each with its own quantity, with no automated inter-location transfers in the first release (e.g., separating **Main Warehouse / Backroom**, **Retail Storefront**, and **RMA / Defective Quarantine**). *(Note: Choose **A** if you strictly operate out of a single room/storefront and don't separate showroom vs. backroom stock).*

## Q6. [architect] Product catalog ownership and data exchange
You said the e-commerce website and in-store POS need live inventory reads and sale deductions. Which system owns product and variant details, including manufacturer part numbers, and what additional data exchange is required?

A. This API owns product and variant details; no other integrations or bulk file exchange are needed beyond the stated e-commerce and POS flows.
B. An existing catalog or ERP owns product details; synchronize it with this API (name the system, fields, and direction).
C. Another existing system owns product details (name it and describe the data and direction).
D. Add file-based bulk import or export (give the format and direction).
E. Use both an external catalog source and file-based bulk import or export (describe them).
X. Other (please specify)

[Answer]: X. Other (please specify)

4. Both system integrations and file import/export are required:

- **System Integration:** Sync stock levels and order deductions with our **E-commerce storefront and POS/billing system**.
- **File Import/Export:** CSV/Excel import to bulk-upload distributor supplier catalogs/invoices, and CSV export for accounting and tax reconciliation.

## Q7. [security engineer] Authentication and access boundaries
Who may call the API, and how must access be separated?

A. Staff from one organization, with roles that control which inventory actions they may perform.
B. Authenticated service clients from one organization, with scoped permissions.
C. Multiple organizations or tenants whose inventory data must be isolated from one another.
D. Use an existing identity provider or access-control model (name it and describe the required permissions).
E. A combination or another access model (describe it).
X. Other (please specify)

[Answer]: E. 5. A combination of staff role-based access and authenticated service clients for one business:

- **Staff Roles:** *Sales/Technicians* (view stock, reserve for orders, receive stock) vs. *Managers/Admins* (create/retire SKUs, perform manual stock write-offs/adjustments).
- **Service Clients:** Scoped API keys/tokens for the E-commerce website and POS system to read stock and create reservations.

## Q8. [operations engineer] Scale and service targets
What measurable service targets should the API meet? Include expected item and location counts, peak concurrent clients or request rate, response-time targets, and availability window where known. If a target is not known, say so rather than guessing.

A. No numeric service targets are known yet; leave them as open questions.
B. Provide expected inventory size and peak request or concurrency targets.
C. Provide response-time and availability targets (include measurement window and percentile where relevant).
D. Provide all known scale, response-time, and availability targets.
E. Use an existing service-level agreement or target (identify it).
X. Other (please specify)

[Answer]: A. 1. No strict numeric service targets are finalized yet; leave exact SLA percentages and peak request-per-second metrics as open questions to be benchmarked based on current catalog size (typically 1,000–10,000 active component SKUs) and checkout traffic.

## Q9. [security engineer] Audit details, retention, and compliance
The first release includes a stock-movement history. What details must each entry retain, for how long, and are any security or compliance standards applicable? If these are not known, say so.

A. Record the actor, time, item and location, quantity change, and reason; retain entries for a specified period.
B. Keep the movement history, with its fields and retention governed by an existing company policy (name it).
C. Meet named security or compliance requirements (identify the standard and applicable requirements).
D. Specify both movement-history details and retention, and named security or compliance requirements.
E. No additional compliance or retention obligations are known beyond the movement history and existing company policy.
X. Other (please specify)

[Answer]: X. Other (please specify)

2. Record every stock change with an immutable audit log capturing **who** (staff user ID or system client), **when** (timestamp), **what changed** (SKU, location, quantity delta), and **why** (reason code: *Sale, Purchase Receipt, Custom Build Allocation, RMA/Defective, Cycle Count Adjustment*). Retain audit history for at least **7 years** to support financial audits, warranty/RMA disputes, and shrinkage investigations.

## Q10. [architect] Manual location transfers
You said there should be no automated transfers between locations in the first release. Should authorized staff still be able to record a manual transfer between locations?

A. Yes — staff with the appropriate role can record a transfer with a source, destination, SKU, quantity, and reason.
B. No — the first release cannot transfer stock between locations; staff may only receive or adjust stock at each location.
C. Only managers may record a manual transfer; no automated transfers are allowed.
D. Use another rule (describe it).
X. Other (please specify)

[Answer]: A. Yes — staff with the appropriate role can record a transfer with a source, destination, SKU, quantity, and reason.

## Q11. [architect] Catalog ownership and identifiers
You specified supplier catalog imports and inventory synchronization with the e-commerce storefront and POS. Which system is authoritative for product and variant details, and are SKU and manufacturer part number separate identifiers?

A. This API owns the product catalog and assigns a SKU; manufacturer part number is stored separately when available.
B. The e-commerce storefront owns the catalog and SKU; this API synchronizes product and variant details from it.
C. The POS or another named catalog system owns the catalog and SKU; this API synchronizes product and variant details from it.
D. Supplier files provide manufacturer part numbers; this API assigns SKUs and maps each imported variant to its supplier part number.
E. A different system or mapping rule applies (name it and describe the identifiers).
X. Other (please specify)

[Answer]: A. This API owns the product catalog and assigns a SKU; manufacturer part number is stored separately when available.

## Q12. [product manager] Reservation lifecycle
When stock is reserved for a custom PC build or an online checkout, what should release or finalize that reservation?

A. Keep it until a staff member or calling system explicitly cancels or fulfills it; no automatic expiry.
B. Expire it automatically after a specified duration (give the duration); allow renewal if needed.
C. Use different rules for custom PC builds and online checkouts (describe each).
D. Convert the reservation to a sale when payment completes; release it on cancellation or expiry (give the expiry rule).
E. Use another lifecycle (describe the events and timing).
X. Other (please specify)

[Answer]: C. Use different rules for custom PC builds and online checkouts (describe each).

## Q13. [operations engineer] Sale and stock-update timing
You said POS and e-commerce sales must deduct stock immediately. What must callers observe when a sale is recorded?

A. The API validates availability and commits the deduction before returning success; the updated quantity is visible to the next read.
B. Sale events may update stock asynchronously, within a maximum delay (specify the delay).
C. The sale first creates or consumes a reservation, and fulfillment completes the stock deduction (describe the expected read behavior).
D. Use another consistency rule (describe it).
X. Other (please specify)

[Answer]: A. The API validates availability and commits the deduction before returning success; the updated quantity is visible to the next read.

## Q14. [release engineer] Invoice import and stock receipt
When a distributor invoice is imported, should it change on-hand inventory, and should someone approve the receipt first?

A. Import catalog details separately; an invoice creates a pending stock receipt that a manager approves before quantities change.
B. An invoice import immediately records a stock receipt and increases on-hand quantities.
C. Import invoice data for reference only; staff record received quantities separately.
D. Use another import and approval flow (describe it).
X. Other (please specify)

[Answer]: A. Import catalog details separately; an invoice creates a pending stock receipt that a manager approves before quantities change.

## Q15. [architect] Import formats and row errors
Which file formats must be accepted for supplier catalog and invoice imports, and how should the API handle invalid or duplicate rows?

A. Accept CSV only; reject the whole upload if any row is invalid or duplicated.
B. Accept CSV and Excel workbooks; import valid rows and return a row-by-row error report for invalid or duplicate rows.
C. Accept CSV and Excel workbooks; show a validation preview and require confirmation before importing, with row-by-row errors.
D. Use another format or validation approach (describe it).
X. Other (please specify)

[Answer]: C. Accept CSV and Excel workbooks; show a validation preview and require confirmation before importing, with row-by-row errors.

## Q16. [product manager] Reservation expiry and release rules
You selected different reservation rules for custom PC builds and online checkouts. For each, when should the reservation be released or finalized, and should it expire automatically? Give the online-checkout hold duration if one applies.

A. Custom-build reservations remain until a technician completes or cancels the build; online-checkout reservations expire after a specified duration (give it) or on cancellation, and payment finalizes the sale.
B. Both reservation types remain until explicitly fulfilled or cancelled; neither expires automatically.
C. Both reservation types expire automatically, but after different durations (give each duration and release event).
D. Use another lifecycle (describe release, expiry, and fulfillment for each reservation type).
X. Other (please specify)

[Answer]: A. Custom-build reservations remain until a technician completes or cancels the build; online-checkout reservations expire after a specified duration (give it) or on cancellation, and payment finalizes the sale.

## Q17. [product manager] Quarantined stock availability
Should stock in the `RMA / Defective Quarantine` location be excluded from available-to-sell quantities, and what must happen before it can return to saleable stock?

A. Exclude it from available-to-sell; a manager may move it back only after inspection or repair.
B. Exclude it from available-to-sell; quarantined stock cannot be returned to saleable stock in the first release.
C. Exclude it from available-to-sell and require a recorded disposition such as repair, return to supplier, or scrap before closing the quarantine.
D. Use another rule (describe it).
X. Other (please specify)

[Answer]: C. Exclude it from available-to-sell and require a recorded disposition such as repair, return to supplier, or scrap before closing the quarantine.

## Q18. [architect] Duplicate sale requests
If the POS or e-commerce system retries the same sale request after a timeout, should the API prevent the same sale from deducting stock twice?

A. Yes — require a stable sale or order reference and do not apply the same deduction more than once.
B. No — every request is a separate stock deduction; the calling system prevents duplicates.
C. Use another duplicate-request rule (describe it).
X. Other (please specify)

[Answer]: A. Yes — require a stable sale or order reference and do not apply the same deduction more than once.

## Q19. [product manager] Online-checkout reservation duration
You chose for online-checkout reservations to expire automatically. How long should the API hold stock for an unpaid checkout before releasing the reservation? State the duration and, if relevant, whether a customer action such as starting payment extends it.

A. Use a fixed duration from reservation creation (give the duration).
B. Use a fixed duration that can be extended by a customer action (give the duration and action).
C. Do not set a duration yet; leave it as an open question for checkout-traffic benchmarking.
D. Use another expiry rule (describe it).
X. Other (please specify)

[Answer]: C. Do not set a duration yet; leave it as an open question for checkout-traffic benchmarking.

## Q20. [product manager] Manual-transfer audit reason
You require a reason for each manual location transfer, but the reason codes you listed do not name transfers. How should the immutable movement history identify a transfer?

A. Add a dedicated `Location Transfer` reason code for the source and destination stock movements.
B. Use one of the existing reason codes for transfers (name it and explain how the transfer is distinguished).
C. Keep the listed reason codes and require a free-text reason for each transfer.
D. Use another audit rule (describe it).
X. Other (please specify)

[Answer]: A. Add a dedicated `Location Transfer` reason code for the source and destination stock movements.

## Q21. [product manager] Manual-transfer permissions
You said Sales/Technicians can view, reserve, and receive stock, while Managers/Admins can adjust stock. Since a transfer changes stock at two locations, which staff role may record or approve it?

A. Sales/Technicians and Managers/Admins may record transfers; no separate approval is required.
B. Only Managers/Admins may record transfers.
C. Sales/Technicians may request transfers, but a Manager/Admin must approve before quantities change.
D. Use another role or approval rule (describe it).
X. Other (please specify)

[Answer]: A. Sales/Technicians and Managers/Admins may record transfers; no separate approval is required.
