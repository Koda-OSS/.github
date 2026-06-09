# `.koda` directory schema

Generic contract for the [Koda](https://koda.sh) docs root. Koda reads `.koda/kdx.json` first; page metadata lives in the manifest, content lives in markdown files.

## Layout

| Role | Location | Purpose |
|---|---|---|
| Manifest | `.koda/kdx.json` | Corpus, directories, categories, pages |
| Schema | `.koda/kdx.schema.json` | Optional JSON Schema for the manifest |
| Guide | `.koda/koda.schema.md` | This file |
| Pages | `.koda/docs/` | camelCase `.md` content files |
| Media | `.koda/media/` | Static assets (not indexed as pages) |

**Read order:** `kdx.json` → this file → page content at `pages[].path`.

## `kdx.json`

| Field | Required | Role |
|---|---|---|
| `version` | yes | Manifest version |
| `corpus` | yes | ~1 paragraph project summary for autocomplete |
| `directories` | yes | Doc roots relative to project root |
| `categories` | yes | Flat nav taxonomy |
| `pages` | yes | Page metadata and content file paths |

```json
{
  "path": ".koda/docs",
  "attributes": ["type::md", "mode::primary"]
}
```

Directory attributes: one `type::` (`md` | `mdx`) and one `mode::` (`primary` | `fallback`).

## Pages

Each entry in `pages` declares metadata; the markdown file at `path` is content only.

```json
{
  "slug": "page-slug",
  "path": ".koda/docs/pageName.md",
  "title": "Page Title",
  "category": "category-id",
  "attributes": ["::type::guide"],
  "search": {
    "semanticSummary": "Search summary.",
    "keywords": ["keyword"]
  }
}
```

- `slug` — URL identity; use for cross-links: `[Topic](page-slug)`
- `category` — must match a `categories[].id` (lowercase; hyphens, dots, or colons allowed)
- `attributes` — page tags (`::type::`, domain tags); not `::category::`
- `title` — must match the `#` heading in the markdown file

## Markdown files

Pure markdown. No frontmatter or exported metadata blocks.

```md
# Page Title

Content…
```

## Invariants

- Every `pages[].category` exists in `categories`.
- Every category has at least one page.
- `pages[].slug` values are unique.
- `pages[].path` files exist.
- Page `title` matches file `#` heading.
- No source code, build output, or secrets under `.koda/`.

## Agent checklist

- [ ] New page: add `pages[]` entry and create `.md` file
- [ ] New category: add to `categories` and reference from `pages[].category`
- [ ] Cross-links use slugs
- [ ] Run `npm run check:kdx`
