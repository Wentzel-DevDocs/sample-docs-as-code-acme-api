---
title: Shipments
description: The shipment object and its endpoints — create, list, retrieve, purchase, and cancel.
order: 1
status: stable
audience: developers
category: reference
product: Acme Parcel API
owners: api-docs
last_reviewed: 2026-03-10
---

# Shipments

A shipment represents one parcel moving from a sender to a recipient. Creating a shipment fetches [rates](/reference/rates); purchasing it with a chosen rate produces a label and a [tracker](/reference/tracking).

## The shipment object

| Field | Type | Description |
| --- | --- | --- |
| `id` | string | Unique ID, prefixed `shp_` |
| `object` | string | Always `"shipment"` |
| `status` | string | `draft`, `purchased`, or `cancelled` |
| `from_address` | object | Sender [address](/reference/addresses) |
| `to_address` | object | Recipient [address](/reference/addresses) |
| `parcel` | object | `length_cm`, `width_cm`, `height_cm` (numbers), `weight_g` (integer) |
| `rates` | array | Available [rates](/reference/rates); populated on creation |
| `selected_rate` | object | The purchased rate; `null` until purchase |
| `tracking_number` | string | Carrier tracking number; `null` until purchase |
| `label_url` | string | Time-limited URL to the label file; `null` until purchase |
| `created` | integer | Unix timestamp (seconds) |

## Create a shipment

`POST /v1/shipments`

| Parameter | Required | Description |
| --- | --- | --- |
| `from_address` | yes | Sender address object |
| `to_address` | yes | Recipient address object |
| `parcel` | yes | Dimensions and weight |

```bash
curl https://api.acmeparcel.example/v1/shipments \
  -H "Authorization: Bearer ak_test_51JqXo2example" \
  -H "Content-Type: application/json" \
  -d '{ "from_address": { "name": "Warehouse 3", "street1": "400 Dock Rd", "city": "Reno", "state": "NV", "postal_code": "89502", "country": "US" },
        "to_address": { "name": "Pat Doe", "street1": "18 Elm St", "city": "Austin", "state": "TX", "postal_code": "78701", "country": "US" },
        "parcel": { "length_cm": 30, "width_cm": 20, "height_cm": 10, "weight_g": 900 } }'
```

Returns the shipment with `status: "draft"` and its `rates`. Rates expire after 24 hours; recreate the shipment if they lapse.

## List shipments

`GET /v1/shipments` — supports [pagination](/guides/pagination) plus a `status` filter (`draft`, `purchased`, `cancelled`).

## Retrieve a shipment

`GET /v1/shipments/{id}`

## Purchase a shipment

`POST /v1/shipments/{id}/purchase` with `{ "rate_id": "rate_..." }` buys the label at the chosen rate. Use an `Idempotency-Key` header so retries never double-buy (see [Errors](/guides/errors#idempotency)). Fails with `422` if the shipment is not in `draft` or the rate has expired, and emits `shipment.purchased`.

## Cancel a shipment

`POST /v1/shipments/{id}/cancel` cancels a `draft`, or requests a carrier refund for a `purchased` shipment whose label is unused (see the [billing FAQ](/faq/billing)). Emits `shipment.cancelled`.
