---
title: Reference overview
description: The resource model, object envelope, and a map of every endpoint in the Acme Cloud API.
nav_order: 1
---
The API exposes five resource types. Each has a short ID prefix so you can tell objects apart at a glance, and every object carries the same envelope fields.

## Resources

| Resource | ID prefix | What it is |
| --- | --- | --- |
| [Project](/api/projects) | `proj_` | The container for deployments, domains, and webhook endpoints |
| [Deployment](/api/deployments) | `dep_` | One build-and-release of a project |
| [Domain](/api/domains) | `dom_` | A custom domain attached to a project |
| [API key](/api/api-keys) | `key_` | A credential with a scope |
| [Webhook endpoint](/api/webhook-endpoints) | `we_` | A URL subscribed to event types |

Events delivered to webhooks use the prefix `evt_`.

## The object envelope

Every object has at least:

| Field | Type | Description |
| --- | --- | --- |
| `id` | string | Unique ID, prefixed by type |
| `object` | string | The type name, e.g. `"project"` |
| `created` | integer | Unix timestamp of creation |

Timestamps everywhere in the API are Unix seconds, UTC.

## Endpoint map

| Method and path | Action |
| --- | --- |
| `POST /v1/projects` | Create a project |
| `GET /v1/projects` / `GET /v1/projects/:id` | List / retrieve projects |
| `PATCH /v1/projects/:id` / `DELETE /v1/projects/:id` | Update / delete a project |
| `POST /v1/deployments` | Create a deployment |
| `GET /v1/deployments` / `GET /v1/deployments/:id` | List / retrieve deployments |
| `POST /v1/deployments/:id/cancel` | Cancel a deployment |
| `POST /v1/domains` / `GET /v1/domains` | Add / list domains |
| `POST /v1/domains/:id/verify` / `DELETE /v1/domains/:id` | Re-check / remove a domain |
| `POST /v1/api_keys` / `GET /v1/api_keys` / `DELETE /v1/api_keys/:id` | Create / list / revoke keys |
| `POST /v1/webhook_endpoints` / `GET /v1/webhook_endpoints` | Create / list endpoints |
| `PATCH /v1/webhook_endpoints/:id` / `DELETE /v1/webhook_endpoints/:id` | Update / delete an endpoint |
| `GET /v1/webhook_endpoints/:id/deliveries` | Inspect recent deliveries |

All list endpoints share the same [pagination](/guides/pagination) contract, and all errors share the same [envelope](/guides/errors).
