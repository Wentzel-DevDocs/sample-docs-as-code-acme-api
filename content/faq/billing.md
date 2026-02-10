---
title: Billing FAQ
description: How label purchases are billed, what happens on carrier reweighs, and how refunds for unused labels work.
order: 2
status: stable
audience: all
category: troubleshooting
product: Acme Parcel API
owners: api-docs
last_reviewed: 2026-02-10
---

# Billing FAQ

## When am I charged?

At purchase time. Each successful call to the [purchase endpoint](/reference/shipments#purchase-a-shipment) bills the selected rate's `amount` to your account's payment method. Test-mode purchases are never billed.

## What is a reweigh adjustment?

Carriers weigh and measure parcels in their network. If a parcel is heavier or larger than declared, the carrier bills the difference, which appears on your invoice as an adjustment referencing the shipment ID. Declaring accurate `parcel` dimensions and weight when [creating shipments](/reference/shipments) is the only reliable way to avoid adjustments.

## How do refunds for unused labels work?

Cancel the purchased shipment before the label is first scanned. The refund request goes to the carrier, which typically confirms within 1–14 days depending on the carrier; the outcome arrives as a `refund.succeeded` or `refund.failed` [event](/reference/events). Confirmed refunds are credited against your next invoice. Labels that have been scanned are not refundable.

## Where are my invoices?

Invoices are issued monthly and available in the dashboard under **Billing → Invoices**, itemized per shipment with any adjustments and refunds. There is no invoices API endpoint today.

## How do I dispute a charge?

Email `billing@acmeparcel.example` with the shipment ID and invoice number within 60 days of the invoice date. For reweigh disputes, include your own weight or dimension evidence (a photo of the parcel on a scale is usually sufficient).
