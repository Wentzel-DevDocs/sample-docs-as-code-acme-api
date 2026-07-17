---
title: Deployments
description: Create a deployment from a source archive, follow it through queued, building, ready, or error, and cancel it if needed.
nav_order: 3
---
A deployment is one build-and-release of a project. Creating one hands Acme a source archive; Acme builds it and, on success, atomically switches the project's traffic to it.

## The deployment object

| Field | Type | Description |
| --- | --- | --- |
| `id` | string | e.g. `dep_3fQz9x` |
| `object` | string | Always `"deployment"` |
| `project` | string | Owning project ID |
| `source_url` | string | Where the source archive was fetched from |
| `status` | string | `queued`, `building`, `ready`, `error`, or `canceled` |
| `error_message` | string or null | Populated when `status` is `error` |
| `created` / `finished` | integer / null | Unix timestamps |

## Lifecycle

```text
queued -> building -> ready
                   -> error
(queued or building) -> canceled
```

`ready` and `error` are terminal, and `ready` means the deployment is serving traffic - it becomes the project's `active_deployment`. Subscribe to `deployment.succeeded` and `deployment.failed` [webhooks](/guides/webhooks) instead of polling for the transition.

## Create a deployment

`POST /v1/deployments` - requires the `write` scope. This endpoint is the poster child for [idempotency keys](/guides/idempotency); use one.

| Body parameter | Required | Description |
| --- | --- | --- |
| `project` | yes | The project to deploy |
| `source_url` | yes | HTTPS URL of a `.tar.gz` source archive, fetchable by Acme for at least 15 minutes |

```bash
curl https://api.acmecloud.example/v1/deployments \
  -H "Authorization: Bearer $ACME_KEY" \
  -H "Content-Type: application/json" \
  -H "Idempotency-Key: $(uuidgen)" \
  -d '{ "project": "proj_8s2Kd1", "source_url": "https://builds.example.com/storefront-42.tar.gz" }'
```

## List, retrieve, filter

`GET /v1/deployments` supports two filters alongside standard [pagination](/guides/pagination):

| Query parameter | Description |
| --- | --- |
| `project` | Only deployments of this project |
| `status` | Only deployments in this state |

```bash
curl "https://api.acmecloud.example/v1/deployments?project=proj_8s2Kd1&status=error&limit=10" \
  -H "Authorization: Bearer $ACME_KEY"
```

## Cancel a deployment

`POST /v1/deployments/:id/cancel` stops a `queued` or `building` deployment; it ends in state `canceled` and never receives traffic. Canceling a deployment that is already terminal returns `409 conflict_error`. There is no way to cancel `ready` - deploy a newer build instead.
