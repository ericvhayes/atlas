---
name: lite-orient
description: >
  Write or refresh a topic's orientation pack — the dense, self-contained
  briefing that Atlas Lite publishes for M365 Copilot grounding. Use when the
  user says "orient <topic>", "create orientation pack", "publish <topic> to
  lite", or "refresh the orientation".
allowed-tools:
  - Bash
  - Read
  - Write
  - Edit
---

## What an orientation pack is

`topics/<slug>/orientation.md` — a briefing someone (or Copilot's retrieval)
can read cold. It is retrieved in CHUNKS by a semantic index, so every
section must stand alone.

## Writing rules (they matter for retrieval quality)

- **≤ 2,000–3,000 words.** Longer packs dilute chunk relevance.
- **Front-load a summary block**: 3–5 sentences covering what the topic is,
  where it stands, and why it matters — before any heading.
- **Self-contained H2 sections** that each repeat the topic name and enough
  context to be understood alone: `## <Topic> — what it is`,
  `## <Topic> — current state`, `## <Topic> — key decisions`,
  `## <Topic> — open questions`, `## <Topic> — glossary`.
- **Decisions get dates and one-line rationale.** Facts over narrative.
- When the decisions section outgrows ~10 entries, split it into
  `topics/<slug>/decisions.md` and keep a pointer.
- No secrets, credentials, or content inappropriate for the M365 index —
  the pack WILL be published once flagged.

## Steps

1. Read the topic: `.topic.yaml`, `notes.md`, `manifest.yaml` (what data
   files exist), and search for related context: `axis search "<topic>"`.
2. Write `topics/<slug>/orientation.md` per the rules above.
3. Ask the user to confirm publication, then set `lite_publish: true` in
   `topics/<slug>/.topic.yaml`.
4. Commit both files — the exporter publishes from committed state only.
5. Run `axis lite export` and report what was written.
