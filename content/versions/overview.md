---
title: API versioning
description: How Acme Parcel versions the HTTP API and these docs for enterprise integrators.
order: 1
status: stable
audience: developers
category: concept
product: Acme Parcel API
owners: api-docs
last_reviewed: 2026-05-12
---

# API versioning

The major version is in the URL path: `/v1`. Backwards-compatible additive changes do not bump the major version.

| Version | Status | Notes |
| --- | --- | --- |
| v1 | current | Documented on this site |
| v0 | retired | See [v0 migration](/versions/v0-migration) |

Breaking changes require a new major path and a deprecation window announced in the [changelog](/changelog).
