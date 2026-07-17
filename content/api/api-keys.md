---
title: API keys
description: Mint scoped keys programmatically, watch last_used_at, and revoke keys - secrets are revealed exactly once.
nav_order: 5
---
API keys authenticate every request; the mechanics of using them are covered in [Authentication](/guides/authentication). This page covers managing keys via the API itself.

## The api_key object

| Field | Type | Description |
| --- | --- | --- |
| `id` | string | e.g. `key_2mVp7r` |
| `object` | string | Always `"api_key"` |
| `name` | string | A label you choose, e.g. `"ci-pipeline"` |
| `scope` | string | `read`, `write`, or `admin` |
| `secret` | string | **Only present in the creation response** |
| `last_four` | string | Last 4 characters of the secret, for identification |
| `allowed_ips` | array | Optional CIDR allowlist (up to 20 entries) |
| `last_used_at` | integer or null | Unix timestamp of the most recent request |
| `created` | integer | Unix timestamp |

## Create a key

`POST /v1/api_keys` - requires the `admin` scope.

| Body parameter | Required | Description |
| --- | --- | --- |
| `name` | yes | Label shown in listings |
| `scope` | yes | `read`, `write`, or `admin` |
| `allowed_ips` | no | CIDR blocks the key may be used from |

```bash
curl https://api.acmecloud.example/v1/api_keys \
  -H "Authorization: Bearer $ACME_ADMIN_KEY" \
  -H "Content-Type: application/json" \
  -d '{ "name": "ci-pipeline", "scope": "write", "allowed_ips": ["203.0.113.0/24"] }'
```

The response is the only time `secret` is ever returned - store it immediately. Every later retrieval shows `last_four` only; a lost secret means minting a new key.

## List keys

`GET /v1/api_keys` returns your keys (without secrets), newest first. `last_used_at` is the field to watch during [rotation](/guides/authentication#rotating-a-key): when it stops advancing, nothing is using the key anymore.

## Revoke a key

`DELETE /v1/api_keys/:id` - requires the `admin` scope. Revocation propagates within 60 seconds, is permanent, and fires an `api_key.revoked` [webhook event](/guides/webhooks). A key cannot revoke itself; use a different `admin` key, which conveniently guarantees you never lock yourself out with an API call.
