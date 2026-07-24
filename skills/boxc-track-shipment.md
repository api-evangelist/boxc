---
name: Track a shipment
description: Retrieve tracking events for a BoxC tracking number.
api: openapi/boxc-openapi-original.yml
operations: [getTrackingEvents]
auth: OAuth 2.0 bearer JWT (scope read_shipments)
base_url: https://api.boxc.com/v1
---

# Track a shipment

Use this flow to fetch the tracking history for a shipment.

## Steps
1. **Get tracking events.** Call `getTrackingEvents` (`GET /track/{trackingNumber}`) with the shipment's tracking number. The response returns an ordered list of tracking events.

## Notes
- Each event includes a scan status/event code (e.g. code 250 = PROOF OF DELIVERY, added v1.120) and, since v1.120, `longitude` and `latitude`.
- Tracking events are displayed in the **scan location's local timezone** (other datetimes in the API are UTC).
- To receive tracking updates as they happen instead of polling, subscribe to the `shipments_status` webhook topic (see skills/boxc-subscribe-webhooks.md).
- Rate limits and the `{status, code, message, errors}` envelope apply as documented in conventions/boxc-conventions.yml.
