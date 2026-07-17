# sample-acme-api

**Medium** enterprise docs-as-code sample for [hosted.devdocs.ai](https://hosted.devdocs.ai).

Documents **Acme Parcel**, a fictional shipping REST API: guides, field-level reference, FAQ, changelog, and a real OpenAPI 3 contract published beside the Markdown.

| Scale | Nav | Pages | Extra artifacts |
| --- | --- | ---: | --- |
| Medium | Two-level | 17 | `llms.txt`, `openapi.yaml` |

## Why this looks like an enterprise API docs repo

- Diátaxis-ish structure: **Guides** (how-to) + **API Reference** + **FAQ** + **Changelog**
- OpenAPI 3.0 in `openapi/openapi.yaml`, shipped as a publish artifact
- Code samples, parameter tables, and webhook signature guidance for retrieval demos
- Page metadata (`status`, `audience`, `owners`, `category`, `last_reviewed`)
- CI quality gate on every PR; publish from `main` when a push token is configured
- `CODEOWNERS` routes `/content/reference/` to an API docs team handle

## Repo layout

```
content/
  index.md
  changelog.md
  guides/                  how-to guides + _section.json
  reference/               resource reference + OpenAPI page
  faq/
openapi/openapi.yaml       machine-readable API contract
docs.config.json           includes extraArtifacts → openapi.yaml
scripts/check.mjs
scripts/publish.mjs
.github/                   quality + publish workflows, CODEOWNERS, PR template
CONTRIBUTING.md
STYLEGUIDE.md
```

## Content map

| Section | Pages |
| --- | --- |
| Root | Introduction, API changelog |
| Guides | Quickstart, Authentication, Pagination, Errors, Rate limits, Webhooks |
| API Reference | OpenAPI, Shipments, Rates, Addresses, Tracking, Webhook endpoints, Events |
| FAQ | General, Billing |

## Local workflow

```bash
npm ci
npm test
PUSH_TOKEN=hdpt_xxx SLUG=acmecloud npm run publish   # optional
```

`docs.config.json` → `extraArtifacts` copies `openapi/openapi.yaml` into the publish payload as `openapi.yaml`.

## One-time product setup

1. Sign up at `https://hosted.devdocs.ai/signup`.
2. Claim a subdomain (for example `acmecloud`).
3. Create a push token and set `HOSTED_DOCS_PUSH_TOKEN` in repo secrets for CI.

## Verify after publish

Ask the chat widget: **How do I verify a webhook signature?**

Also confirm `https://<slug>.hosted.devdocs.ai/openapi.yaml` is reachable after publish.

## Related samples

| Repo | Intent |
| --- | --- |
| [hello-docs](https://github.com/Wentzel-DevDocs/sample-docs-as-code-hello-docs) | Simple / flat |
| [acme-api](https://github.com/Wentzel-DevDocs/sample-docs-as-code-acme-api) | Medium / API + OpenAPI |
| [nimbus-platform](https://github.com/Wentzel-DevDocs/sample-docs-as-code-nimbus-platform) | Complex / platform |
