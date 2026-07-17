---
title: Domains
description: Attach a custom domain to a project, verify ownership with a TXT record, and understand the verification states.
nav_order: 4
---
By default a project serves at `<name>.acmecloud-sites.example`. Attaching a custom domain points your own hostname at the project instead.

## The domain object

| Field | Type | Description |
| --- | --- | --- |
| `id` | string | e.g. `dom_5tXw8q` |
| `object` | string | Always `"domain"` |
| `project` | string | Owning project ID |
| `hostname` | string | e.g. `www.example.com` |
| `status` | string | `pending`, `verified`, or `failed` |
| `verification_token` | string | The value to put in your TXT record |
| `created` | integer | Unix timestamp |

## Add a domain

`POST /v1/domains` - requires the `write` scope.

| Body parameter | Required | Description |
| --- | --- | --- |
| `project` | yes | The project to attach the domain to |
| `hostname` | yes | The fully-qualified hostname |

The new domain starts in `pending`, and the response includes the `verification_token`.

## Verify ownership

Create a DNS TXT record at `_acme-verify.<hostname>` whose value is the `verification_token`:

```text
_acme-verify.www.example.com.  TXT  "acme-verify=tok_9dKe2mQx"
```

Acme re-checks DNS every few minutes for 72 hours. You can force an immediate re-check with `POST /v1/domains/:id/verify`. On success the status flips to `verified` and a `domain.verified` [webhook event](/guides/webhooks) fires; if 72 hours pass without a matching record, the status becomes `failed` and `domain.failed` fires. A `failed` domain can be retried with the same verify endpoint after you fix the record.

You also need routing DNS - a CNAME from your hostname to `<project>.acmecloud-sites.example` - but only the TXT record gates verification.

## List and remove

`GET /v1/domains?project=proj_8s2Kd1` lists a project's domains with standard [pagination](/guides/pagination). `DELETE /v1/domains/:id` detaches the domain immediately; traffic to the hostname stops resolving to the project as caches expire.
