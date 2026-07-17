---
title: Quickstart
description: Create your first shipment, choose a rate, buy a label, and fetch tracking — a complete end-to-end walkthrough in test mode.
order: 1
status: stable
audience: developers
category: how-to
product: Acme Parcel API
owners: api-docs
last_reviewed: 2026-07-01
---

# Quickstart

This walkthrough uses test mode, so nothing is charged and no real label is produced. You need a test API key (`ak_test_...`); see [Authentication](/guides/authentication) for where to find it.

## 1. Create a shipment

A shipment bundles a sender address, a recipient address, and a parcel. Creating one automatically fetches rates from the connected carriers.

```bash
curl https://api.acmeparcel.example/v1/shipments \
  -H "Authorization: Bearer ak_test_51JqXo2example" \
  -H "Content-Type: application/json" \
  -H "Idempotency-Key: qs-demo-001" \
  -d '{
    "from_address": {
      "name": "Warehouse 3", "street1": "400 Dock Rd",
      "city": "Reno", "state": "NV", "postal_code": "89502", "country": "US"
    },
    "to_address": {
      "name": "Pat Doe", "street1": "18 Elm St",
      "city": "Austin", "state": "TX", "postal_code": "78701", "country": "US"
    },
    "parcel": { "length_cm": 30, "width_cm": 20, "height_cm": 10, "weight_g": 900 }
  }'
```

The response is a `shipment` with `status: "draft"` and a `rates` array:

```json
{
  "id": "shp_9f2c1ab4",
  "object": "shipment",
  "status": "draft",
  "rates": [
    { "id": "rate_7d1e", "carrier": "AcmePost", "service": "ground", "amount": 785, "currency": "usd", "est_days": 4 },
    { "id": "rate_a410", "carrier": "Roadrunner Express", "service": "two_day", "amount": 1490, "currency": "usd", "est_days": 2 }
  ]
}
```

Amounts are integers in the currency's minor unit (785 means $7.85).

## 2. Buy a label

Purchase the shipment by choosing one of its rates:

```bash
curl https://api.acmeparcel.example/v1/shipments/shp_9f2c1ab4/purchase \
  -H "Authorization: Bearer ak_test_51JqXo2example" \
  -H "Content-Type: application/json" \
  -d '{ "rate_id": "rate_7d1e" }'
```

The shipment's status becomes `purchased`, and the response now includes `tracking_number` and a `label_url` (a PDF by default; see the [FAQ](/faq/general) for ZPL).

## 3. Track the package

```bash
curl https://api.acmeparcel.example/v1/trackers/TRK1002938471 \
  -H "Authorization: Bearer ak_test_51JqXo2example"
```

In test mode, trackers advance through a canned lifecycle (`pre_transit` → `in_transit` → `delivered`) over a few minutes so you can exercise your integration. Details are in the [Tracking reference](/reference/tracking).

## 4. Stop polling — use webhooks

Register a webhook endpoint and Acme Parcel will POST you a `tracker.updated` event each time a package moves. Start with the [Webhooks guide](/guides/webhooks).
