---
name: Calculate landed cost (duties and taxes)
description: Validate a destination address and calculate the landed cost of a shipment.
api: openapi/boxc-openapi-original.yml
operations: [validateAddress, calculateDuty]
auth: OAuth 2.0 bearer JWT (scopes validate, calculate)
base_url: https://api.boxc.com/v1
---

# Calculate landed cost (duties and taxes)

Use this flow to preview a cross-border shipment's landed cost (duties + taxes) before shipping.

## Prerequisites
- A token minted with the `calculate` scope (required for the Calculate Duty resource since v1.118) and, for address validation, the `validate` scope.

## Steps
1. **Validate the destination address.** Call `validateAddress` (`POST /validate-address`) with the recipient address to normalize/verify it and catch address errors (code 1012 / 1425) early.
2. **Calculate landed cost.** Call `calculateDuty` (`POST /calculate-duty`) with the destination, currency, and line items (value + HS code) to get the estimated duties and taxes, i.e. the landed cost.

## Notes
- `terms` (DDU/DDP/DAP) determine who bears duties/taxes; DDP means the shipper prepays landed cost.
- HS code / description are required for customs line items throughout BoxC (error 1215/1224 on missing or invalid codes).
- Exchange-rate lookups can fail transiently (code 1051) — retry.
