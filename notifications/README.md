# Zoho Billing Notification Inventory

Local record of the notification/workflow-alert configuration currently set in Zoho Billing for
**Om Arham Social Welfare Foundation** (organization_id `60036596424`), pulled via the Zoho Billing
MCP connector on 2026-08-18. Use this to review/edit locally; once changes are agreed we can push
them back to Zoho Billing (create/update/delete workflow alerts, or update product email templates).

Full machine-readable data: [`zoho-billing-notifications-inventory.json`](./zoho-billing-notifications-inventory.json)

## What this covers vs. what it doesn't

Zoho Billing has two different kinds of "notifications":

1. **Custom Workflow alerts** (Settings > Automation > Workflows) - these ARE readable via the API
   and are fully inventoried below.
2. **Standard built-in transactional emails** per module (Invoice sent/overdue, Payment
   received/failed/refunded, Subscription trial-ending/dunning/renewal, Credit Note, Quote, etc. -
   configured under Settings > Automation > Email Notifications) - these are **not exposed** by any
   tool available through this connector. There's no list/get endpoint for them, and the one
   related write tool (`Update Product Email Templates`) needs a `product_id` we have no way to
   look up here. **These need to be reviewed manually in the Zoho Billing UI** if you want them
   audited/changed.

So: the table below is a complete list of *custom* alerts, not a complete list of every
notification Zoho Billing can send.

## Current custom workflow alerts (as of 2026-08-18)

| Workflow | Entity | Status | Trigger events | Action type | Recipients | Notes |
|---|---|---|---|---|---|---|
| ADYTrainingManagement (`2278898000002540071`) | Subscriptions | Inactive | reactivated, unpaid, deleted, renewed, expired, cancelled, downgraded, upgraded, activation | Webhook (not email) | N/A - webhook | "Do Not Modify - Ashish" |
| FullEvent (`2278898000003168229`) | Subscriptions | **Active** | resumed, paused, billing_date_changed, cancellation scheduled/removed, reactivated, unpaid, renewed, expired, cancelled, downgraded, upgraded, activation | Webhook (not email) | N/A - webhook | "Do Not Modify - Ashish"; 0 executions in last 3 months |
| unpaid to live (`2278898000003957556`) | Subscriptions | Inactive | Fires when subscription_status changes from `unpaid` -> `live` | Webhook (not email) | N/A - webhook | Conditional rule, not "all records" |

No custom alerts exist for **Invoice, Customer, Credit Note, Payment, or Quote** entities.

Notably: **none of the three configured alerts are actual email alerts** - all three deliver a
webhook payload only. If the org expects subscription-lifecycle emails to go out, none currently
exist as custom alerts (they'd either need to come from the standard built-in notifications noted
above, or a new email-type alert would need to be created).

## Recommended items to review/decide

- [ ] Confirm with Ashish (or current data owner) whether `ADYTrainingManagement` and
      `unpaid to live` are still needed, or should be deleted/reactivated.
- [ ] Investigate why `FullEvent` (the only active alert) has zero executions in the last 3
      months - verify its webhook endpoint is still valid/receiving traffic.
- [ ] Decide whether standard built-in email notifications (payment, invoice, subscription,
      reports) need a manual audit in the Zoho Billing UI, since they're outside API visibility.
- [ ] Decide whether any entity (Invoice/Customer/Payment/Credit Note/Quote) actually needs a new
      email-type workflow alert, since none exist today.

## Once decisions are made

I can push changes back via:
- `Create an Email Alert` / `Update an Email Alert` / `Delete an Email Alert` / `Mark an Email Alert as Active|Inactive` for workflow-based alerts.
- `Update Product Email Templates` for per-product template/recipient overrides (needs a product_id).
