---
title: Authentication
description: How Acme Parcel API keys work — test vs live mode, the Authorization header, key rotation, and common auth errors.
order: 2
status: stable
audience: developers
category: how-to
product: Acme Parcel API
owners: api-docs
last_reviewed: 2026-07-16
---

# Authentication

The API authenticates every request with an API key passed as a bearer token. Requests without a valid key receive `401 Unauthorized`.

## API keys

Keys are created in the dashboard under **Developers → API keys**. Each key belongs to exactly one mode:

| Prefix | Mode | Behavior |
| --- | --- | --- |
| `ak_test_` | Test | No real labels, no charges; trackers simulate movement |
| `ak_live_` | Live | Real carrier labels; purchases are billed |

Test and live data are fully separate: a shipment created with a test key is invisible to live keys, and vice versa.

## Sending the key

Pass the key in the `Authorization` header on every request:

```bash
curl https://api.acmeparcel.example/v1/shipments \
  -H "Authorization: Bearer ak_live_9tR2example"
```

Keys must never appear in URLs, query strings, or client-side code. Treat a key like a password: server-side only, stored in a secret manager or environment variable.

## Rotating a key

Create a second key, deploy it, confirm traffic has moved, then revoke the old one from the dashboard. Revocation is immediate; requests with a revoked key fail with `authentication_error`.

## Auth failures

| Status | Error type | Meaning |
| --- | --- | --- |
| `401` | `authentication_error` | Missing, malformed, revoked, or wrong-mode key |
| `403` | `permission_error` | Valid key, but it lacks access to the resource |

The error envelope and every error type are documented in [Errors](/guides/errors).
