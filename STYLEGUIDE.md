# Style guide (Acme Parcel sample)

## API reference pages

- Lead with the resource object table, then list endpoints.
- Use consistent HTTP verb + path headings (`## Create a shipment`).
- Document error codes that are specific to the endpoint; link to [Errors](/guides/errors) for the shared envelope.

## Code samples

- Default to `curl` with bearer tokens.
- Show both request and trimmed response JSON.
- Use `ak_test_...` keys only.

## Terms

| Prefer | Avoid |
| --- | --- |
| shipment | order (ambiguous) |
| label | sticker |
| test mode | sandbox (except in FAQ synonyms) |
| API key | token (unless discussing webhook signing secrets) |
