# Wiki Schema

This file defines the structure, conventions, and workflows for this wiki.
The LLM reads this to understand how to maintain the wiki consistently.

## Structure

```
wiki/
├── schema.md         # This file — structure and conventions
├── index.md          # Master index of all pages (auto-maintained)
├── log.md            # Chronological record of all operations
├── overview.md       # High-level synthesis of everything in the wiki
├── raw/              # Immutable source documents
│   └── assets/       # Images and attachments from sources
└── pages/            # LLM-maintained wiki pages
    ├── entities/     # People, teams, systems, services, APIs
    ├── concepts/     # Ideas, patterns, principles, methodologies
    ├── sources/      # Summaries of each ingested source document
    ├── comparisons/  # Side-by-side analyses, trade-off tables
    └── analyses/     # Deep dives, investigations, synthesized findings
```

## Page Conventions

### Frontmatter

Every page starts with YAML frontmatter:

```yaml
---
title: Page Title
category: entities | concepts | sources | comparisons | analyses
tags: [relevant, tags]
sources: [source-filename.md]
created: YYYY-MM-DD
updated: YYYY-MM-DD
---
```

### Cross-References

Use Obsidian-compatible wikilinks: `[[Page Title]]` or `[[Page Title|display text]]`.

When creating or updating a page:
1. Link to all related pages using `[[wikilinks]]`
2. Check if the linked page exists — if not, create a stub
3. Update the linked page's "See Also" section to link back

### Page Format

```markdown
---
(frontmatter)
---

# Page Title

(Main content — facts, analysis, synthesis)

## Key Points

- Concise bullet points summarizing the page

## See Also

- [[Related Page 1]]
- [[Related Page 2]]

## Sources

- [[source-filename]] — what was extracted from this source
```

## Workflows

### Ingest a New Source

1. Read the source document from `raw/`
2. Create a summary page in `pages/sources/`
3. Extract entities → create/update pages in `pages/entities/`
4. Extract concepts → create/update pages in `pages/concepts/`
5. Update cross-references across all affected pages
6. Update `index.md` with new entries
7. Update `overview.md` if the new source changes the big picture
8. Append an entry to `log.md`

### Answer a Query

1. Read `index.md` to find relevant pages
2. Read the relevant pages
3. Synthesize an answer with `[[citations]]`
4. If the answer is valuable, file it as a new page in `pages/analyses/`
5. Append a query entry to `log.md`

### Lint / Health Check

Look for and report:
- Broken `[[wikilinks]]` (target page doesn't exist)
- Orphan pages (no inbound links)
- Pages missing from `index.md`
- Thin pages (< 50 characters of content)
- Pages missing frontmatter
- Stale content (contradicted by newer sources)
- Missing cross-references between related pages

## Rules

1. **Never modify files in `raw/`** — sources are immutable
2. **Always update `index.md`** after creating or renaming a page
3. **Always update `log.md`** after any operation
4. **Use wikilinks** for all cross-references
5. **One concept per page** — split if a page covers too much
6. **Prefer updating over duplicating** — if content belongs on an existing page, update it
