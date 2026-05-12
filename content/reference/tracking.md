---
title: Tracking
description: The tracker object, tracking statuses, the tracking events array, and how tracker.updated webhooks relate to polling.
order: 4
status: stable
audience: developers
category: reference
product: Acme Parcel API
owners: api-docs
last_reviewed: 2026-05-12
---

# Tracking

Every purchased [shipment](/reference/shipments) gets a tracker that mirrors the carrier's scan events. Read it on demand, or subscribe to `tracker.updated` [webhooks](/guides/webhooks) to be pushed changes.

## The tracker object

| Field | Type | Description |
| --- | --- | --- |
| `id` | string | Unique ID, prefixed `trk_` |
| `object` | string | Always `"tracker"` |
| `tracking_number` | string | The carrier tracking number |
| `carrier` | string | Carrier name |
| `status` | string | Current status; see the table below |
| `est_delivery` | integer | Estimated delivery, Unix timestamp; `null` if unknown |
| `events` | array | Scan history, oldest first |

Each entry in `events` has `status`, `description`, `location`, and `time`.

## Statuses

| Status | Meaning |
| --- | --- |
| `pre_transit` | Label bought; carrier has not scanned the package yet |
| `in_transit` | Moving through the carrier network |
| `out_for_delivery` | On a vehicle for final delivery |
| `delivered` | Delivered |
| `returned` | Returned to sender |
| `failure` | Carrier reported a terminal problem |

## Retrieve a tracker

`GET /v1/trackers/{tracking_number}`

```bash
curl https://api.acmeparcel.example/v1/trackers/TRK1002938471 \
  -H "Authorization: Bearer ak_test_51JqXo2example"
```

```json
{
  "id": "trk_44aa",
  "object": "tracker",
  "tracking_number": "TRK1002938471",
  "carrier": "AcmePost",
  "status": "in_transit",
  "est_delivery": 1767571200,
  "events": [
    { "status": "pre_transit", "description": "Label created", "location": "Reno, NV", "time": 1767225600 },
    { "status": "in_transit", "description": "Departed facility", "location": "Reno, NV", "time": 1767312000 }
  ]
}
```

## Push instead of poll

Polling trackers burns your [rate limit](/guides/rate-limits). Prefer subscribing to `tracker.updated`, which fires on every status or event change and carries the full tracker in `data.object`. In test mode, trackers advance through the lifecycle automatically over a few minutes.
