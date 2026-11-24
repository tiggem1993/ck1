# Inventory Management API Personas

## Inventory Staff

- **Role:** Internal staff, represented by Sales Associates and Technicians.
- **Goals:** Check location-specific availability, reserve stock for customer orders or custom builds, receive stock, and transfer stock between locations.
- **Context:** Uses the API through staff-facing business tools. The persona combines the two staff groups as requested, while stories preserve their different capabilities.
- **Permissions:** May view stock, reserve stock, receive stock, and record manual transfers. Technicians complete or cancel custom builds and record their custom-build fulfillment. Repair disposition is recorded as a permitted transfer; Managers/Admins retain catalog administration and stock adjustment/write-off permissions, including supplier-return and scrap deductions.
- **Pain points:** Stale availability, accidental over-allocation, and stock movements that cannot be reconciled.

## Managers/Admins

- **Role:** Staff responsible for catalog stewardship and inventory control.
- **Goals:** Maintain product and variant records, correct or write off stock, manage supplier data and invoice receipts, and prepare accounting/tax exports.
- **Context:** Uses authorized staff operations for administrative and control workflows.
- **Permissions:** May create, update, and retire product/variant details; perform manual stock adjustments/write-offs, including supplier-return and scrap dispositions; record manual transfers; preview and confirm supplier catalog/invoice imports; approve imported invoice receipts; and request CSV exports. Other staff may view catalog details but may not maintain them.
- **Pain points:** Uncontrolled catalog changes, unapproved invoice quantities affecting stock, and incomplete audit evidence.

## E-commerce Client

- **Role:** Authenticated service client for the e-commerce storefront.
- **Goals:** Read current stock, reserve available units during checkout, and record order fulfillment and sale deductions.
- **Context:** Integrates through scoped credentials; checkout reservation expiry is configurable but its duration remains open for benchmarking.
- **Permissions:** Only the operations granted to its service-client scopes, including stock reads, reservations, and sale deductions. It records fulfillment for its online orders.
- **Pain points:** Checkout success with stale stock, duplicate deductions on retries, or reservations that do not release correctly.

## POS Client

- **Role:** Authenticated service client for the point-of-sale system.
- **Goals:** Read current stock, reserve available units, and record fulfilled sales without overselling.
- **Context:** Integrates through scoped credentials and requires committed inventory results to be visible on the next read.
- **Permissions:** Only the operations granted to its service-client scopes, including stock reads, reservations, and sale deductions. It records fulfillment for its orders.
- **Pain points:** Selling unavailable units, duplicate deductions, or seeing stale inventory after a sale.

## Persona Relationships and Priority

- Inventory Staff and Managers/Admins operate within one business and share location-level inventory records; their permissions differ by operation.
- E-commerce Client and POS Client are separate system personas with distinct credentials and system-specific acceptance criteria, even where their permitted operations overlap.
- Inventory Staff and Managers/Admins are the primary operational personas. E-commerce Client and POS Client are equally important integration personas for checkout and sales workflows.
- Technician-specific build completion and fulfillment remain explicit capabilities within the combined Inventory Staff persona rather than a separate persona.
