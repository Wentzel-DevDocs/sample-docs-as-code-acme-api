---
title: API changelog
description: Notable Acme Parcel API changes that affect integrators — new fields, webhook events, and deprecations.
order: 4
status: stable
audience: developers
category: reference
product: Acme Parcel API
owners: api-docs
last_reviewed: 2026-02-10
---

# API changelog

This sample changelog shows how enterprise API docs keep a single, reviewable history next to reference pages. Dates are fictional.

## 2026-06-15 — Webhook signature header freeze

- `Acme-Signature` remains the only verification header.
- Documented retry schedule for failed deliveries (see [Webhooks](/guides/webhooks)).

## 2026-05-02 — Rates include `delivery_days`

- `Rate` objects now include optional `delivery_days` (integer).
- Clients should ignore unknown fields.

## 2026-03-18 — Idempotency on purchase

- `POST /shipments/{id}/buy` accepts `Idempotency-Key`.
- Duplicate keys within 24 hours return the original purchase response.

## Compatibility policy

Backwards-compatible changes ship without a URL version bump. Breaking changes require a new major path prefix and a minimum 180-day deprecation window announced here and on the status page.
