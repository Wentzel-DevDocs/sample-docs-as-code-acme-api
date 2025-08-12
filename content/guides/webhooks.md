---
title: Webhooks
description: Receive shipment and tracking events by HTTPS push — endpoint setup, the event payload, signature verification, and retry behavior.
order: 6
status: stable
audience: developers
category: how-to
product: Acme Parcel API
owners: api-docs
last_reviewed: 2025-08-12
---

# Webhooks

Webhooks push [events](/reference/events) to your server as they happen, so you never have to poll. When a package moves, Acme Parcel POSTs a JSON event to every endpoint subscribed to that event type.

## 1. Register an endpoint

Create a [webhook endpoint](/reference/webhook-endpoints) pointing at an HTTPS URL you control:

```bash
curl https://api.acmeparcel.example/v1/webhook_endpoints \
  -H "Authorization: Bearer ak_test_51JqXo2example" \
  -H "Content-Type: application/json" \
  -d '{ "url": "https://example.com/hooks/acme", "enabled_events": ["tracker.updated", "shipment.purchased"] }'
```

The response includes a signing `secret` (`whsec_...`). Store it — you need it to verify deliveries.

## 2. The delivery payload

Deliveries are `POST` requests whose body is a single event object:

```json
{
  "id": "evt_1b8c33d0",
  "object": "event",
  "type": "tracker.updated",
  "created": 1767225600,
  "data": { "object": { "id": "trk_44aa", "status": "out_for_delivery" } }
}
```

Respond with any `2xx` within 10 seconds. Do heavy work asynchronously — acknowledge first, process after.

## 3. Verify the signature

Every delivery includes an `X-Acme-Signature` header of the form `t=<unix seconds>,v1=<hex hmac>`. The HMAC is SHA-256 over the string `"<t>.<raw request body>"` using your endpoint's secret:

```js
import { createHmac, timingSafeEqual } from "node:crypto";

function verifyAcmeSignature(header, rawBody, secret) {
  const parts = Object.fromEntries(header.split(",").map((p) => p.split("=")));
  const expected = createHmac("sha256", secret).update(`${parts.t}.${rawBody}`).digest("hex");
  return timingSafeEqual(Buffer.from(expected), Buffer.from(parts.v1));
}
```

Reject deliveries whose signature does not match or whose timestamp `t` is more than five minutes old (replay protection). Verify against the **raw** body bytes — parsing and re-serializing JSON will change the string and break the HMAC.

## 4. Retries and ordering

Failed deliveries (non-2xx or timeout) are retried with exponential backoff for up to 24 hours, then dropped. Deliveries can arrive out of order and, rarely, more than once — treat handlers as idempotent, keyed on the event `id`. If you miss deliveries, backfill by listing [events](/reference/events).
