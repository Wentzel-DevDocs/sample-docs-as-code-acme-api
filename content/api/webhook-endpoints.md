---
title: Webhook endpoints
description: Create and manage the URLs Acme delivers events to, rotate signing secrets, and inspect recent deliveries.
nav_order: 6
---
A webhook endpoint is a URL plus a list of event types. How events are signed, verified, and retried is covered in the [Webhooks guide](/guides/webhooks); this page is the management API.

## The webhook_endpoint object

| Field | Type | Description |
| --- | --- | --- |
| `id` | string | e.g. `we_4nRs6t` |
| `object` | string | Always `"webhook_endpoint"` |
| `project` | string | Owning project ID |
| `url` | string | HTTPS URL events are POSTed to |
| `events` | array | Subscribed event types, or `["*"]` for all |
| `secret` | string | Signing secret (`whsec_...`) - **only present in the creation response** |
| `disabled` | boolean | Disabled endpoints receive no deliveries |
| `created` | integer | Unix timestamp |

## Create an endpoint

`POST /v1/webhook_endpoints` - requires the `write` scope.

| Body parameter | Required | Description |
| --- | --- | --- |
| `project` | yes | The project whose events to receive |
| `url` | yes | HTTPS only; must respond within 10 seconds |
| `events` | yes | Array of [event types](/guides/webhooks#event-types), or `["*"]` |

```bash
curl https://api.acmecloud.example/v1/webhook_endpoints \
  -H "Authorization: Bearer $ACME_KEY" \
  -H "Content-Type: application/json" \
  -d '{ "project": "proj_8s2Kd1", "url": "https://hooks.example.com/acme", "events": ["deployment.succeeded", "deployment.failed"] }'
```

The `secret` in the response is shown once - it is what you verify signatures with.

## Update, disable, rotate

`PATCH /v1/webhook_endpoints/:id` accepts `url`, `events`, and `disabled`. Setting `"disabled": true` pauses deliveries without losing configuration; missed events are **not** replayed on re-enable. Passing `"rotate_secret": true` returns a new `secret` (again, shown once) - the old secret keeps validating for 24 hours so you can roll over gracefully.

## Inspect deliveries

`GET /v1/webhook_endpoints/:id/deliveries` lists the last 7 days of delivery attempts with standard [pagination](/guides/pagination):

```json
{
  "object": "list",
  "data": [
    {
      "id": "del_8wYu3v",
      "event": "evt_7pLm2c",
      "attempt": 2,
      "status_code": 500,
      "succeeded": false,
      "created": 1767312060
    }
  ],
  "has_more": false
}
```

This is the first place to look when your handler seems to be missing events: it shows exactly what Acme sent, when, and how your server answered.

## Delete an endpoint

`DELETE /v1/webhook_endpoints/:id` stops deliveries immediately and is permanent. Pending retries for past events are dropped.
