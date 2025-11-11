---
title: Rates
description: The rate object, how rates are generated when a shipment is created, and the standalone rate quote endpoint.
order: 2
status: stable
audience: developers
category: reference
product: Acme Parcel API
owners: api-docs
last_reviewed: 2025-11-11
---

# Rates

A rate is a priced shipping option from one carrier for one parcel. Rates are generated automatically when you create a [shipment](/reference/shipments); they can also be quoted standalone without creating anything.

## The rate object

| Field | Type | Description |
| --- | --- | --- |
| `id` | string | Unique ID, prefixed `rate_` |
| `object` | string | Always `"rate"` |
| `carrier` | string | Carrier name, for example `AcmePost`, `Roadrunner Express`, `GlobalFreight` |
| `service` | string | Carrier service level, for example `ground`, `two_day`, `overnight` |
| `amount` | integer | Price in the currency's minor unit (cents for USD) |
| `currency` | string | Lowercase ISO 4217 code, for example `usd` |
| `est_days` | integer | Estimated transit days |
| `expires` | integer | Unix timestamp after which the rate cannot be purchased |

## Standalone quotes

`POST /v1/rates` takes the same `from_address`, `to_address`, and `parcel` parameters as shipment creation and returns only a `rates` array. Use it for checkout price previews when you are not ready to create a shipment.

```bash
curl https://api.acmeparcel.example/v1/rates \
  -H "Authorization: Bearer ak_test_51JqXo2example" \
  -H "Content-Type: application/json" \
  -d '{ "from_address": { "postal_code": "89502", "country": "US" },
        "to_address": { "postal_code": "78701", "country": "US" },
        "parcel": { "length_cm": 30, "width_cm": 20, "height_cm": 10, "weight_g": 900 } }'
```

Standalone quotes accept these abbreviated addresses (postal code and country only); full addresses tighten the price. Quoted rates are estimates until purchased — carriers may adjust for reweighs, as described in the [billing FAQ](/faq/billing).

## Choosing a rate

Rates within a shipment are sorted cheapest first. Filter client-side on `carrier`, `service`, or `est_days`, then pass the chosen `rate_id` to the [purchase endpoint](/reference/shipments#purchase-a-shipment).
