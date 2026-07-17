---
title: Addresses
description: The address object used across the API and the standalone address validation endpoint.
order: 3
status: stable
audience: developers
category: reference
product: Acme Parcel API
owners: api-docs
last_reviewed: 2026-07-01
---

# Addresses

Addresses appear inline inside [shipments](/reference/shipments) and rate quotes. They can also be validated standalone before you create anything.

## The address object

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `name` | string | yes | Person or company name |
| `company` | string | no | Company, when `name` is a person |
| `street1` | string | yes | First street line |
| `street2` | string | no | Apartment, suite, unit |
| `city` | string | yes | City or locality |
| `state` | string | US/CA | State or province code, for example `TX` |
| `postal_code` | string | yes | Postal or ZIP code |
| `country` | string | yes | Two-letter ISO 3166-1 code, for example `US` |
| `phone` | string | some services | Required by carriers for express services |

## Validate an address

`POST /v1/addresses/validate` checks deliverability and normalizes formatting without creating a resource:

```bash
curl https://api.acmeparcel.example/v1/addresses/validate \
  -H "Authorization: Bearer ak_test_51JqXo2example" \
  -H "Content-Type: application/json" \
  -d '{ "street1": "18 elm street", "city": "austin", "state": "TX", "postal_code": "78701", "country": "US", "name": "Pat Doe" }'
```

```json
{
  "valid": true,
  "normalized": { "street1": "18 ELM ST", "city": "AUSTIN", "state": "TX", "postal_code": "78701-3204", "country": "US", "name": "Pat Doe" },
  "messages": []
}
```

When `valid` is `false`, `messages` lists human-readable problems (unknown street, missing unit number, and so on). Validation is advisory: you may still create a shipment with an unvalidated address, but undeliverable packages are returned at the sender's cost.

## Practical tips

- Validate at address-entry time in your checkout, not at label-purchase time.
- Store the `normalized` form; carriers match it more reliably.
- International shipments require `phone` for most express services.
