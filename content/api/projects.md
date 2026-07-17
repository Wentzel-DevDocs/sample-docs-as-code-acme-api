---
title: Projects
description: Create, list, update, and delete projects - the container object for deployments, domains, and webhooks.
nav_order: 2
---
A project is the top-level container. Deployments, domains, and webhook endpoints all belong to exactly one project.

## The project object

| Field | Type | Description |
| --- | --- | --- |
| `id` | string | e.g. `proj_8s2Kd1` |
| `object` | string | Always `"project"` |
| `name` | string | 3-64 chars, lowercase letters, digits, and hyphens; unique per account |
| `created` | integer | Unix timestamp |
| `active_deployment` | string or null | The `dep_` ID currently serving traffic, if any |

## Create a project

`POST /v1/projects` - requires the `write` scope.

| Body parameter | Required | Description |
| --- | --- | --- |
| `name` | yes | The project name (see constraints above) |

```bash
curl https://api.acmecloud.example/v1/projects \
  -H "Authorization: Bearer $ACME_KEY" \
  -H "Content-Type: application/json" \
  -d '{ "name": "storefront" }'
```

A name that is already taken fails with `409 conflict_error`, code `name_taken`.

## List and retrieve

`GET /v1/projects` returns projects newest-first with standard [pagination](/guides/pagination). `GET /v1/projects/:id` retrieves one:

```json
{
  "id": "proj_8s2Kd1",
  "object": "project",
  "name": "storefront",
  "created": 1767225600,
  "active_deployment": "dep_3fQz9x"
}
```

## Update a project

`PATCH /v1/projects/:id` accepts `name` only. Renaming does not change the project `id`, and in-flight deployments are unaffected.

## Delete a project

`DELETE /v1/projects/:id` - requires the `admin` scope. Deletion is permanent and cascades to the project's domains and webhook endpoints. A project with a deployment currently in state `queued` or `building` cannot be deleted (`409 conflict_error`); [cancel it](/api/deployments) first. Deleting fires a `project.deleted` [webhook event](/guides/webhooks).
