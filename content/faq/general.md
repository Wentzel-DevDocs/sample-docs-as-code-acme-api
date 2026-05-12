---
title: General FAQ
description: Common questions about carriers, test mode, label formats, refunds, and getting support.
order: 1
status: stable
audience: all
category: troubleshooting
product: Acme Parcel API
owners: api-docs
last_reviewed: 2026-05-12
---

# General FAQ

## Which carriers are supported?

AcmePost, Roadrunner Express, and GlobalFreight, all through one integration. Every [rate](/reference/rates) names its `carrier` and `service`, so you can filter to the carriers you have negotiated terms with.

## How real is test mode?

Test keys (`ak_test_`) exercise the full API surface: shipments, rates, purchases, webhooks. Nothing is charged, no real label is generated, and [trackers](/reference/tracking) advance through a simulated lifecycle over a few minutes. Test and live data never mix.

## What format are labels?

`label_url` points at a PDF sized 4×6 inches by default. Pass `"label_format": "zpl"` when [purchasing](/reference/shipments#purchase-a-shipment) to receive raw ZPL for thermal printers instead. Label URLs are time-limited; download and store the file if you need it beyond a few days.

## Can I get a refund for an unused label?

Yes. Call [cancel](/reference/shipments#cancel-a-shipment) on a purchased shipment whose label has not been scanned. The carrier confirms asynchronously — you will receive a `refund.succeeded` or `refund.failed` [event](/reference/events). Details and timelines are in the [billing FAQ](/faq/billing).

## Why does my shipment have no rates?

Usually one of: the parcel exceeds every carrier's limits for the lane, the destination country is unserved, or an address is malformed enough that carriers rejected the quote. [Validate the addresses](/reference/addresses) and check the parcel dimensions; the shipment's `rates` array will list whatever was quotable.

## How do I contact support?

Email `support@acmeparcel.example` and include the `request_id` from the [error envelope](/guides/errors) — it lets us find the exact request.
