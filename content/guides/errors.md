---
title: Errors
description: The Acme Parcel error envelope, the HTTP status codes the API returns, every error type, and guidance on retries and idempotency.
order: 4
status: stable
audience: developers
category: how-to
product: Acme Parcel API
owners: api-docs
last_reviewed: 2025-11-11
---

# Errors

The API signals failure with conventional HTTP status codes and a single JSON error envelope, so one error handler can cover every endpoint.

## The error envelope

<!-- include: error-envelope.md -->

```json
{
  "error": {
    "type": "invalid_request_error",
    "code": "parameter_missing",
    "message": "to_address.postal_code is required.",
    "param": "to_address.postal_code",
    "request_id": "req_5f0d0f6f0a"
  }
}
```

`param` is present when a specific field caused the problem. Always log `request_id`; support uses it to find the request.

## Status codes

| Status | Meaning |
| --- | --- |
| `400` | The request was malformed or failed validation |
| `401` | Missing or invalid API key |
| `402` | The purchase could not be completed (for example, insufficient balance) |
| `403` | The key is valid but not allowed to do this |
| `404` | No such resource, or it belongs to the other mode |
| `409` | Conflict — usually an [idempotency](#idempotency) key reused with a different body |
| `422` | The request was valid JSON but the operation is impossible (for example, purchasing a cancelled shipment) |
| `429` | Rate limited — see [Rate limits](/guides/rate-limits) |
| `500` | Something failed on Acme Parcel's side |

## Error types

| Type | Typical status | Description |
| --- | --- | --- |
| `invalid_request_error` | 400, 404, 422 | Bad parameters or an impossible operation |
| `authentication_error` | 401 | Key problems |
| `permission_error` | 403 | Key lacks access |
| `idempotency_error` | 409 | Idempotency key reuse conflict |
| `rate_limit_error` | 429 | Too many requests |
| `api_error` | 500 | Internal failure — safe to retry with backoff |

## Idempotency

Any `POST` may include an `Idempotency-Key` header (any unique string up to 255 characters). Retrying with the same key and the same body returns the original result instead of repeating the side effect — essential for purchases:

```bash
curl https://api.acmeparcel.example/v1/shipments/shp_9f2c1ab4/purchase \
  -H "Authorization: Bearer ak_live_9tR2example" \
  -H "Idempotency-Key: order-8812-label" \
  -d '{ "rate_id": "rate_7d1e" }'
```

Keys are remembered for 24 hours. Reusing a key with a **different** body returns `409 idempotency_error`.

## Retry guidance

Retry `429` and `5xx` with exponential backoff and jitter, always carrying the same `Idempotency-Key` on POSTs. Do not retry `4xx` validation errors — fix the request instead.
