---
title: Pagination
description: How list endpoints paginate with cursor parameters — limit, starting_after, and the has_more flag.
order: 3
status: stable
audience: developers
category: how-to
product: Acme Parcel API
owners: api-docs
last_reviewed: 2026-07-01
---

# Pagination

Every list endpoint (`GET /shipments`, `GET /events`, and so on) uses cursor pagination. Responses share one envelope:

```json
{
  "data": [ { "id": "shp_9f2c1ab4", "object": "shipment" } ],
  "has_more": true
}
```

## Parameters

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| `limit` | integer | `20` | Page size, between 1 and 100 |
| `starting_after` | string | — | An object ID; returns results **after** it in the list |

Lists are ordered newest first. To fetch the next page, pass the `id` of the **last** item of the current page as `starting_after`.

## Walking a full list

```bash
curl "https://api.acmeparcel.example/v1/shipments?limit=100" \
  -H "Authorization: Bearer ak_test_51JqXo2example"

# then, using the last id from the previous page:
curl "https://api.acmeparcel.example/v1/shipments?limit=100&starting_after=shp_0c77e2aa" \
  -H "Authorization: Bearer ak_test_51JqXo2example"
```

Stop when `has_more` is `false`. Because the cursor is an object ID rather than an offset, pages stay consistent even while new objects are being created.

## Filtering

List endpoints accept resource-specific filters alongside pagination — for example `GET /shipments?status=purchased` or `GET /events?type=tracker.updated`. Filters are documented per endpoint in the [API Reference](/reference/shipments).
