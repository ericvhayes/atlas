# Memory Schema

This file defines the format and rules for the shared memory system.
All memory lives inside Atlas at `.ai/memory/` — one location, all tools.

## File: .ai/memory/MEMORY.md

**Purpose**: Curated working scratchpad — durable facts, active threads, decisions.
**Cap**: 2,500 characters. Enforced by the agent before each write.
**Lifecycle**: Frozen snapshot — loaded at session start, writes persist to disk
but take effect next session.

### Format

```markdown
<!-- Cap: 2,500 chars. Maintained by AI tools via memory-write skill. -->
# Working Memory

## Active Threads
- (Current work items, open questions, in-progress tasks)

## Environment Notes
- (URLs, tool versions, project structure facts, config details)

## Pending Decisions
- (Decisions that need to be made, options being considered)
```

### Rules

1. Maximum 2,500 characters — check before writing
2. No duplicates — scan for substring match before adding
3. Consolidate when over cap — merge similar entries, remove stale ones
4. Sections are fixed — always use Active Threads, Environment Notes, Pending Decisions
5. Entries should be single-line bullet points (concise facts, not paragraphs)

---

## File: .ai/memory/USER.md

**Purpose**: User preferences and profile — how the user likes to work.
**Cap**: 1,375 characters.

### Format

```markdown
<!-- Cap: 1,375 chars. Updated when AI learns user preferences. -->
# User Profile

## About
- (User's role, team, expertise areas)

## Preferences
- (Communication style, level of detail, tooling preferences)

## Working Style
- (How they like to collaborate with AI — ask first vs. just do it, etc.)
```

---

## File: .ai/memory/daily/{YYYY-MM-DD}.md

**Purpose**: Daily session log — tracks what happened in each session.
**Format**: One file per day, numbered session blocks.

```markdown
#### Session N
**Goal**: (one line — what the user wanted to accomplish)
**Deliverables**: (files created or modified)
**Decisions**: (key decisions and rationale)
**Open threads**: (anything left unfinished)
```

**Rules**: Log silently as work happens. Never announce "I've logged that."

---

## File: .ai/memory/transcripts/{YYYY-MM-DD}.md

**Purpose**: Condensed transcript summaries captured by session hooks.
**Format**: Timestamped entries, first 500 chars of each assistant response.
**Indexed by**: MemSearch (if installed) for vector search retrieval.
**Committed to git**: Yes — lives in Atlas, your personal AI home.
