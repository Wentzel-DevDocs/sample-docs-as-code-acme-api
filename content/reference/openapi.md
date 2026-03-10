---
title: OpenAPI specification
description: Machine-readable OpenAPI 3 description of the Acme Parcel API for code generation, contract tests, and API explorers.
order: 0
status: stable
audience: developers
category: reference
product: Acme Parcel API
owners: api-docs
last_reviewed: 2026-03-10
---

# OpenAPI specification

The Acme Parcel API is described in OpenAPI 3.0. The canonical file in this repository is [`openapi/openapi.yaml`](https://github.com/Wentzel-DevDocs/sample-docs-as-code-acme-api/blob/main/openapi/openapi.yaml) and is also published as a site artifact at `/openapi.yaml`.

## Why we keep OpenAPI next to Markdown

Enterprise docs-as-code teams usually pair:

- **Narrative docs** (guides, concepts, FAQs) written in Markdown for humans and AI retrieval.
- **Contracts** (OpenAPI, AsyncAPI, JSON Schema) for SDK generation, mock servers, and CI contract tests.

Both are versioned in the same Git repository and reviewable in the same pull request.

## Download

| Artifact | Location |
| --- | --- |
| Source in Git | `openapi/openapi.yaml` |
| Published artifact | `https://<slug>.hosted.devdocs.ai/openapi.yaml` |

## Example: list operation

```yaml
/shipments:
  get:
    summary: List shipments
    parameters:
      - name: limit
        in: query
        schema: { type: integer, default: 20 }
```

Human-oriented field tables for the same resources live under [Shipments](/reference/shipments), [Rates](/reference/rates), and [Webhook endpoints](/reference/webhook-endpoints).
