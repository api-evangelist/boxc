---
name: Create and label an international shipment
description: Rate, create, and generate a label for a cross-border BoxC shipment.
api: openapi/boxc-openapi-original.yml
operations: [getEntryPoints, getEstimate, addShipment, addLabel, getLabelsById]
auth: OAuth 2.0 bearer JWT (scopes read_shipments, write_shipments)
base_url: https://api.boxc.com/v1
---

# Create and label an international shipment

Use this flow to quote, create, and label an international ecommerce shipment through BoxC.

## Prerequisites
- An OAuth 2.0 access token (JWT) with `write_shipments` scope, sent as `Authorization: Bearer <jwt>`.
- Set `test: true` on the shipment while developing to generate test labels (see sandbox/boxc-sandbox.yml). The `test` flag is immutable once set.

## Steps
1. **Pick an entry point.** Call `getEntryPoints` (`GET /entry-points`) to list active drop-off/origin locations. Capture the `entry_point.id` — it is required to create a shipment and determines available rates and routes.
2. **(Optional) Rate the shipment.** Call `getEstimate` (`GET /estimate`) with the origin, destination, weight and `packages[]` to preview services and cost before committing.
3. **Create the shipment.** Call `addShipment` (`POST /shipments`) with `entry_point.id`, `to`, `from`, `line_items[]` (each with an HS code and description — required, error 1215), weight, and optional `packages[]` for multi-package. Save the returned shipment `id`.
4. **Create the label.** Call `addLabel` (`POST /labels`) referencing the shipment. If the label is not ready immediately you will get error 1207 (`Label isn't ready for download yet`) — retry after a short delay.
5. **Download the label.** Call `getLabelsById` (`GET /labels/{id}`) to retrieve the label (often `application/pdf`) and the tracking number.

## Rules & gotchas
- Every customs line item needs an HS code + description or creation fails with 1215.
- A shipment with uncancelled/processed labels cannot be recreated or deleted (1201); cancel with `cancelLabel` (`PUT /labels/{id}/cancel`) first.
- Multi-package shipments are cancelled with the master tracking number (1208).
- Handle 429 (code 1015) by backing off using the `X-Rate-Limit` / `X-Rate-Requests` headers.
- Errors use the envelope `{status, code, message, errors}` — see errors/boxc-error-codes.yml.
