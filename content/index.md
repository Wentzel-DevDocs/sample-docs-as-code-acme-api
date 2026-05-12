---
title: Introduction
description: The Acme Parcel API is a REST API for creating shipments, buying carrier labels, tracking packages, and receiving delivery webhooks.
order: 0
status: stable
audience: all
category: overview
product: Acme Parcel API
owners: api-docs
last_reviewed: 2026-05-12
---

# Introduction

The Acme Parcel API lets you create shipments, compare carrier rates, buy shipping labels, and track packages from your own systems. It is a conventional REST API: resources are nouns, requests and responses are JSON, and standard HTTP status codes signal success or failure.

![Shipment lifecycle from draft through rates, purchase, transit, and delivery](/assets/diagrams/shipment-lifecycle.svg)

## Base URL and versioning

All requests go to:

```
https://api.acmeparcel.example/v1
```

The major version is part of the path. Backwards-compatible changes (new fields, new event types) ship without a version bump, so build clients that ignore unknown fields.

## Authentication at a glance

Every request needs an API key sent as a bearer token. Keys are prefixed `ak_test_` (test mode) or `ak_live_` (live mode); test mode never buys real labels or charges your account. See [Authentication](/guides/authentication) for details.

## Your first request

List your shipments (an empty list on a fresh account):

```bash
curl https://api.acmeparcel.example/v1/shipments \
  -H "Authorization: Bearer ak_test_51JqXo2example"
```

```json
{ "data": [], "has_more": false }
```

## What to read next

- [Quickstart](/guides/quickstart) — create a shipment, buy a label, and track it in five minutes.
- [Errors](/guides/errors) and [Rate limits](/guides/rate-limits) — how failures are reported and throttled.
- [Webhooks](/guides/webhooks) — get pushed events instead of polling.
- The [API Reference](/reference/shipments) — every resource, field by field.
- The [OpenAPI specification](/reference/openapi) — machine-readable contract for SDKs and CI.
- The [API changelog](/changelog) — what changed for integrators.
