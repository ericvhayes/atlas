---
name: memory-rollup
description: >
  Consolidate memory: distill daily logs into MEMORY.md, then promote durable
  knowledge into the wiki (the long-term memory tier). Use when the user says
  "roll up memory", "consolidate memory", "memory rollup", or roughly weekly
  when many unrolled dailies exist.
allowed-tools:
  - Bash
  - Read
  - Write
  - Edit
---

## The memory hierarchy

session → `.ai/memory/daily/<date>.md` → `.ai/memory/MEMORY.md` (working
set, 2,500-char cap) → `wiki/pages/` (**long-term memory** — durable
knowledge the system keeps for good and Atlas Lite publishes from).

## Steps

1. **Find the unrolled window.** MEMORY.md carries an
   `<!-- last-rollup: YYYY-MM-DD -->` marker; read every
   `.ai/memory/daily/*.md` after that date (all of them if no marker).
2. **Distill into MEMORY.md.** Keep only durable facts: decisions,
   preferences, ongoing commitments, corrections. Drop chatter and
   completed one-offs. Respect the 2,500-char cap — evict the least
   durable line before adding. Update the last-rollup marker.
3. **Promote to the wiki (long-term memory).** For knowledge that should
   outlive the working set — decisions with rationale, concepts, entities,
   recurring procedures — create or update
   `wiki/pages/<category>/<slug>.md` with frontmatter
   (`title`, `category`, `tags`, `created`, `updated`). Add
   `m365_publish: true` ONLY if the user confirms the page may reach the
   M365 index. When the knowledge came from a topic, append the page path
   to that topic's `wiki_promoted` list in `.topic.yaml`.
4. **Maintain the wiki index**: add new pages to `wiki/index.md`.
5. **Prune with consent**: offer to delete dailies older than 30 days that
   are fully rolled up — never delete without the user's yes.
6. Run `axis semantic index`, then commit everything, then run
   `axis lite export` so published wiki pages reach Atlas Lite.
