# Refined Mockups Questions and Plan

## API Experience Design Plan

- **Interface type:** This is an API-only product; no staff-facing application UI or prior rough mockups are in scope. Use text-first interaction flows and request/response state diagrams rather than inventing screens.
- **Primary flows:** Catalog and location stock lookup; staff receipts, adjustments, transfers, and quarantine disposition; custom-build and checkout reservations; E-commerce/POS fulfillment and sale; supplier import preview/confirmation and invoice approval; accounting/tax export.
- **Developer experience:** Make the distinct Inventory Staff, Manager/Admin, E-commerce Client, and POS Client operations visible in examples. Keep open implementation and contract decisions from requirements analysis open for later design.
- **Accessibility and responsiveness:** No native product UI is specified. Any browser-based API reference should follow WCAG 2.1 AA and reflow for mobile, tablet, and desktop; include keyboard, focus, contrast, and screen-reader requirements.
- **Questions:** Confirm preferred API reference/onboarding format, how service-client credential provisioning should be represented, and the preferred error-response guidance.

## Q1. API reference and examples
What developer-facing API reference and examples would be most useful to the E-commerce and POS teams?

A. OpenAPI reference with representative curl examples for both E-commerce and POS flows.
B. A concise quickstart for each client, with separate stock-read, reservation, fulfillment, and sale examples.
C. Both an OpenAPI reference and separate E-commerce/POS quickstarts.
D. Use another documentation format (describe it).
X. Other (please specify)

[Answer]:

## Q2. Service-client credential onboarding
How should the developer experience describe obtaining scoped credentials for E-commerce and POS?

A. Document Manager/Admin-provisioned credentials separately for each client, with the granted scopes shown.
B. Document credential issuance and rotation through an existing identity/key-management platform (name it if known).
C. Include self-service setup in a developer portal, including scope selection.
D. Leave credential issuance and rotation open for Domain Design; show only the scoped-credential requirement.
X. Other (please specify)

[Answer]:

## Q3. API error guidance
What error-response guidance should the API developer experience present?

A. A machine-readable error body with a stable code, actionable message, field-level validation details, request/correlation ID, and documented retry guidance.
B. Follow an existing company API error standard (name it if known).
C. Leave the error-body convention to Contract Design; show only the required outcomes and recovery paths.
D. Use another error convention (describe it).
X. Other (please specify)

[Answer]:
