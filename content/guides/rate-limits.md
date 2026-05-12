---
title: Rate limits
description: Request rate limits per mode, the X-RateLimit response headers, and how to back off correctly on 429 responses.
order: 5
status: stable
audience: developers
category: how-to
product: Acme Parcel API
owners: api-docs
last_reviewed: 2026-05-12
---

# Rate limits

Rate limits protect the platform and the downstream carrier APIs. Limits are applied per API key with a short burst allowance on top of the sustained rate.

## Default limits

| Mode | Sustained | Burst |
| --- | --- | --- |
| Test (`ak_test_`) | 10 requests/second | 25 |
| Live (`ak_live_`) | 50 requests/second | 100 |

Higher live limits are available on request for high-volume shippers — contact support with your expected peak.

## Reading your budget

Every response carries three headers:

| Header | Meaning |
| --- | --- |
| `X-RateLimit-Limit` | Your sustained requests-per-second limit |
| `X-RateLimit-Remaining` | Requests left in the current window |
| `X-RateLimit-Reset` | Unix timestamp (seconds) when the window resets |

## When you exceed the limit

The API returns `429` with a `rate_limit_error` envelope (see [Errors](/guides/errors)) and a `Retry-After` header in seconds:

```http
HTTP/1.1 429 Too Many Requests
Retry-After: 2
X-RateLimit-Remaining: 0
```

Honor `Retry-After`, then resume with exponential backoff and jitter. Spreading batch work (for example, nightly label creation) evenly over time is far more effective than hammering and retrying.

## Reducing request volume

- Use [webhooks](/guides/webhooks) instead of polling trackers.
- Use `limit=100` when [paginating](/guides/pagination) large lists.
- Cache rarely changing data such as validated addresses.
