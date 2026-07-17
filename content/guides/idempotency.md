---
title: Idempotency
description: Send an Idempotency-Key header on POST requests so network retries never create duplicate resources.
nav_order: 6
---
Networks fail at the worst moments: you POST a deployment, the connection drops, and you have no idea whether it was created. Idempotency keys make the retry safe.

## How it works

Send a unique `Idempotency-Key` header on any `POST` request:

```bash
curl https://api.acmecloud.example/v1/deployments \
  -H "Authorization: Bearer $ACME_KEY" \
  -H "Content-Type: application/json" \
  -H "Idempotency-Key: 4b9f6f80-2c1d-4d1b-9a55-note-any-uuid" \
  -d '{ "project": "proj_8s2Kd1", "source_url": "https://builds.example.com/storefront-42.tar.gz" }'
```

The first request with a given key executes normally and its response is stored. Any later request with the **same key and same body** returns the stored response - status code, headers, and body - without executing again, and carries the header `Idempotent-Replay: true` so you can tell.

## Rules

- Keys are scoped per API key and retained for **24 hours**; after that the same key behaves like a fresh request.
- Reusing a key with a **different** body is rejected with `409 conflict_error` - a key identifies one logical operation, not a slot to reuse.
- Use a random UUID v4 per logical operation, generated **before** the first attempt and reused for every retry of it.
- `GET` and `DELETE` requests are naturally idempotent; the header is accepted but has no effect there.

## What to retry

Retry with the same key on network errors, timeouts, and `5xx` responses. Do not retry `4xx` responses other than `429` - they will fail identically. This pairs with the guidance in [Errors](/guides/errors) and [Rate limits](/guides/rate-limits).
