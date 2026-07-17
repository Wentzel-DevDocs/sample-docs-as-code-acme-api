# sample-acme-api

The **medium** sample docs-as-code repo for the `hosted.devdocs.ai` multi-tenant docs product:
15 pages in a two-level nav (Guides, API Reference, FAQ) documenting **Acme Parcel**, a fictional
shipping REST API. The pages carry code blocks, parameter/status tables, and internal cross-links,
which makes this repo the one to use for exercising nav trees, code rendering, and
retrieval + citation quality across sections.

## Repo layout

```
content/
  index.md                     root page (path "index")
  guides/                      _section.json names + orders the group
    quickstart.md  authentication.md  pagination.md  errors.md  rate-limits.md  webhooks.md
  reference/
    shipments.md  rates.md  addresses.md  tracking.md  webhook-endpoints.md  events.md
  faq/
    general.md  billing.md
scripts/publish.mjs            build + publish script
docs.config.json               site title, default slug, sample verification question
.github/workflows/publish.yml  publishes from CI on push to main
```

## One-time account setup (manual, done in the product)

1. Sign up at `https://hosted.devdocs.ai/signup` and verify the email.
2. Claim a subdomain in the dashboard (for example `acmecloud`).
3. Create a **push token** in the dashboard — it starts with `hdpt_` and is shown once.

The token is scoped to the workspace that minted it, and the broker derives the storage path
from the token server-side, so this repo can only ever write its own site.

## Publish

```bash
npm install
PUSH_TOKEN=hdpt_xxx SLUG=<your-subdomain> node scripts/publish.mjs
# or: PUSH_TOKEN=hdpt_xxx SLUG=<your-subdomain> npm run publish
```

- `VERSION` is optional (env var or first CLI arg); it defaults to a timestamp.
- `SLUG` only affects the live URL printed at the end; set `defaultSlug` in `docs.config.json`
  to your claimed subdomain to skip it.
- A successful push (HTTP 200) **automatically becomes the live version**. The script prints the
  broker response (`versionId`, `entries`, `chunksIndexed`) and `https://<slug>.hosted.devdocs.ai`.

After publishing, open the site's chat widget and ask something the docs answer — for example,
"How do I verify a webhook signature?" — to verify grounded citations across sections.

## Build without publishing (no token needed)

```bash
DRY_RUN=1 node scripts/publish.mjs
```

Writes the exact POST body to `dist/push-body.json` and unpacks every artifact under `dist/site/`.

## Publish from CI (the intended model)

Add the push token as a repo secret named `HOSTED_DOCS_PUSH_TOKEN`; the included workflow builds
and publishes on every push to `main`, using the git SHA as the version.

## How the artifact format works

Each publish POSTs `{ manifest, files }` to `https://hosted.devdocs.ai/api/broker/push` with
`Authorization: Bearer <PUSH_TOKEN>`. `manifest.entries` lists every file with its lowercase hex
`sha256` and byte `size` (verified server-side); `files` maps the same paths to base64 bytes.
Artifacts per publish: one `manifest.json` (`{ version, siteTitle, nav }` — folders become
grouping NavEntries, files become leaves), a `<path>.page.json` per page (sanitized `bodyHtml`
with id anchors matching its `toc`), and the verbatim `<path>.md` source that feeds the AI
retrieval index. The root page's path is exactly `index`.

## Editing content

Pages are markdown with frontmatter (`title`, `description`, `order`). Each folder's
`_section.json` (`{ "title": "API Reference", "order": 2 }`) names and orders its nav group.
