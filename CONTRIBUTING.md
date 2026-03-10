# Contributing to Acme Parcel API docs

Docs live in Git beside an OpenAPI contract. Treat documentation PRs like product PRs: small diffs, reviewed changes, CI green.

## Local loop

```bash
npm ci
npm test          # frontmatter/link check + dry-run build
```

## Layout

| Path | Role |
| --- | --- |
| `content/` | Markdown pages (guides, reference, FAQ, changelog) |
| `content/**/_section.json` | Nav group title + order |
| `openapi/openapi.yaml` | OpenAPI 3 contract (published as `openapi.yaml`) |
| `docs.config.json` | Site title, slug, `llms.txt`, extra artifacts |
| `scripts/check.mjs` | Quality gate |
| `scripts/publish.mjs` | Artifact builder + broker push |

## When the API changes

1. Update `openapi/openapi.yaml`.
2. Update the matching `content/reference/*.md` field tables.
3. Add a `content/changelog.md` entry if integrators must act.
4. Ensure sample curl bodies still work against the fictional examples.

## Review bar

- Accurate auth and error examples
- Internal links pass `npm run check`
- Descriptions short enough for AI retrieval snippets (~1–2 sentences)
