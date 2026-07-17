---
title: FAQ
description: Common questions about SDKs, sandbox data, CORS, IP allowlists, and getting help with the Acme Cloud API.
nav_order: 3
---
Quick answers to the questions we hear most.

## Are there official SDKs?

Yes - `@acmecloud/sdk` for Node.js and `acmecloud` for Python. Both are thin wrappers over the [REST API](/api/overview), so every endpoint in these docs is available in both. Anything the SDKs can do, plain HTTP can do too.

## Does test data persist?

No. Test mode (`ac_test_` keys) is a sandbox and is **reset every night at 04:00 UTC**. Do not point anything you care about at test mode; use it for development and CI.

## Can I call the API from a browser?

The API does not send CORS headers, on purpose: your API key would be visible to anyone using the page. Call the API from your backend and pass results to the browser. If you need browser-visible events, use [webhooks](/guides/webhooks) into your backend instead.

## Can I restrict where a key can be used from?

Yes. Each API key can carry an optional IP allowlist (up to 20 CIDR blocks) set at creation time - see [API keys](/api/api-keys). Requests from other addresses fail with `permission_error`.

## How do I know if an outage is on my side or yours?

Check `https://status.acmecloud.example` first. If the status page is green and you are still seeing `api_error` responses, note the `request_id` from the error body and contact support.

## How do I get help?

Email `support@acmecloud.example` with your project ID and, for API issues, the failing request's `request_id`. Test-mode questions are answered on the community forum as well.
