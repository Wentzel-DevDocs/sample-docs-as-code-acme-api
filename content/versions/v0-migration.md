---
title: Migrating from v0
description: Sample migration notes from the retired Acme Parcel v0 API to v1.
order: 2
status: deprecated
audience: developers
category: how-to
product: Acme Parcel API
owners: api-docs
last_reviewed: 2026-07-07
---

# Migrating from v0

Sample migration page for enterprise demos.

1. Replace base URL `https://api.acmeparcel.example/v0` with `/v1`.
2. Move API keys from query string to `Authorization: Bearer` headers.
3. Update webhook signature header from `X-Acme-Sig` to `Acme-Signature`.
4. Re-test idempotent purchases with `Idempotency-Key`.
