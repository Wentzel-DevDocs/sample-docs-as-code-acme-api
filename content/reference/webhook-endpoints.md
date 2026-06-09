---
title: Webhook endpoints
description: Manage webhook endpoints — create, list, update, delete — and rotate their signing secrets.
order: 5
status: stable
audience: developers
category: reference
product: Acme Parcel API
owners: api-docs
last_reviewed: 2026-06-09
---

# Webhook endpoints

A webhook endpoint is an HTTPS URL that receives [event](/reference/events) deliveries. The delivery format, signature scheme, and retry behavior are covered in the [Webhooks guide](/guides/webhooks); this page covers managing the endpoints themselves.

## The webhook endpoint object

| Field | Type | Description |
| --- | --- | --- |
| `id` | string | Unique ID, prefixed `we_` |
| `object` | string | Always `"webhook_endpoint"` |
| `url` | string | HTTPS delivery URL |
| `enabled_events` | array | Event types to deliver, or `["*"]` for all |
| `status` | string | `enabled` or `disabled` |
| `secret` | string | Signing secret (`whsec_...`); returned **only on creation and rotation** |
| `created` | integer | Unix timestamp |

## Create an endpoint

`POST /v1/webhook_endpoints`

| Parameter | Required | Description |
| --- | --- | --- |
| `url` | yes | Must be HTTPS |
| `enabled_events` | yes | Array of event types, or `["*"]` |

The response includes the `secret` — store it immediately; it is not readable later.

## List and retrieve

`GET /v1/webhook_endpoints` (paginated) and `GET /v1/webhook_endpoints/{id}`. The `secret` field is omitted from both.

## Update an endpoint

`POST /v1/webhook_endpoints/{id}` accepts `url`, `enabled_events`, and `status`. Setting `status` to `disabled` pauses deliveries without deleting the endpoint; missed events are **not** replayed on re-enable, so backfill from the [events list](/reference/events) if the gap matters.

## Rotate the secret

`POST /v1/webhook_endpoints/{id}/rotate_secret` returns a new `secret`. The old secret keeps validating deliveries for 24 hours so you can deploy the new one without dropping events.

## Delete an endpoint

`DELETE /v1/webhook_endpoints/{id}` stops deliveries immediately and cannot be undone.

## Test vs live

Endpoints belong to the mode of the key that created them: test-mode endpoints receive only test events. Create one endpoint per mode when you need both.
