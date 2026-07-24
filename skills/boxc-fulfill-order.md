---
name: Import and fulfill an order
description: Create an order in BoxC and advance its fulfillment status.
api: openapi/boxc-openapi-original.yml
operations: [addOrder, getOrders, updateOrderStatus]
auth: OAuth 2.0 bearer JWT (scopes read_orders, write_orders)
base_url: https://api.boxc.com/v1
---

# Import and fulfill an order

Use this flow to push an order into BoxC's fulfillment (Fulfillment by BoxC) and manage its status.

## Steps
1. **Create the order.** Call `addOrder` (`POST /orders`) with `consignee`/`to`, `from`/`consignor`, and unique `line_items[]` (each referencing a product SKU). Line items must be unique (error 1325) and `shop.order_id` must not duplicate an existing one (1326).
2. **List/search orders.** Call `getOrders` (`GET /orders`) to find orders; results are paginated — follow `next_page` via the `page_token` query parameter.
3. **Advance status.** Call `updateOrderStatus` (`POST /orders/status`) to move orders through their fulfillment states.

## Rules & gotchas
- An order cannot be modified in certain states (1321) or deleted once past a state (1322).
- SKUs referenced must exist and be active in the shop (1330 not found, 1331 inactive).
- Unfulfilled orders older than 1 year are deleted; fulfilled orders are archived after 90 days.
- Subscribe to the `orders_status` and `fulfillments_complete` webhook topics to track progress asynchronously.
