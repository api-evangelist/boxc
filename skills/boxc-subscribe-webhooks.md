---
name: Subscribe to and verify webhooks
description: Register a webhook subscription and verify inbound event authenticity.
api: openapi/boxc-openapi-original.yml
operations: [addWebhook, getWebhooks]
auth: OAuth 2.0 bearer JWT (scopes read_webhooks, write_webhooks)
base_url: https://api.boxc.com/v1
---

# Subscribe to and verify webhooks

Use this flow to receive BoxC events (shipment status, labels, orders, manifests, fulfillments) at your endpoint.

## Steps
1. **Create a subscription.** Call `addWebhook` (`POST /webhooks`) with a `topic`, a public HTTPS `address`, and a `key` (16-40 chars). Valid topics: `shipments_status`, `shipments_label`, `orders_status`, `manifests_complete`, `fulfillments_complete`, `fulfillments_update`. The `address` must be RFC-compliant, include a hostname and path, exclude localhost, and be <= 128 chars.
2. **List/confirm subscriptions.** Call `getWebhooks` (`GET /webhooks`) to confirm your registration. Since v1.121 multiple addresses may subscribe to the same topic+user.

## Verifying events
- BoxC POSTs each event to your `address` with headers `User-Agent: BoxC/1.0 Webhook`, `X-BoxC-Topic`, `X-BoxC-Account`, and `X-BoxC-Hmac-SHA256`.
- Recompute the HMAC: `Base64( HMAC_SHA256(payload, key) )` and compare it to `X-BoxC-Hmac-SHA256` to confirm authenticity.
- Respond with an HTTP 2xx within 2 seconds. Non-2xx responses are retried 3 more times within an hour, then evicted. Redirects are not followed.

## Notes
- Subscriptions are scoped to the registering application only (other apps cannot read/modify them).
- Error 1401 = a webhook with that address already exists.
