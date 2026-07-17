---
title: Events
description: The event object, every event type the API emits, and the events list endpoint used for backfill and audit.
order: 6
status: stable
audience: developers
category: reference
product: Acme Parcel API
owners: api-docs
last_reviewed: 2026-07-01
---

# Events

Everything notable that happens in your account produces an event. Events are what [webhooks](/guides/webhooks) deliver, and they remain listable through the API for 30 days — which makes them the backfill and audit mechanism when your endpoint was down.

## The event object

| Field | Type | Description |
| --- | --- | --- |
| `id` | string | Unique ID, prefixed `evt_` |
| `object` | string | Always `"event"` |
| `type` | string | The event type; see the table below |
| `created` | integer | Unix timestamp |
| `data.object` | object | Full snapshot of the resource **after** the change |

## Event types

| Type | Fires when |
| --- | --- |
| `shipment.created` | A shipment is created |
| `shipment.purchased` | A label is bought |
| `shipment.cancelled` | A shipment is cancelled or a refund is requested |
| `tracker.updated` | A tracker's status or events change |
| `refund.succeeded` | A carrier refund for an unused label is confirmed |
| `refund.failed` | A carrier refused the refund |

Build handlers that ignore unknown types — new types are added without notice, which is a backwards-compatible change.

## List events

`GET /v1/events` — supports [pagination](/guides/pagination) plus a `type` filter:

```bash
curl "https://api.acmeparcel.example/v1/events?type=tracker.updated&limit=100" \
  -H "Authorization: Bearer ak_test_51JqXo2example"
```

## Retrieve an event

`GET /v1/events/{id}` returns a single event — handy when reconciling a webhook delivery you could not process.

## Backfill pattern

Store the `created` timestamp of the last event you processed. After downtime, list events with `limit=100`, walk pages until you pass that timestamp, and process anything unseen, deduplicating on the event `id` (the same rule as webhook handlers, since deliveries may repeat).
