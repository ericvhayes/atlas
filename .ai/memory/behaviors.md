# Memory Behaviors

Universal rules for how AI tools interact with the shared memory system.
All memory lives in Atlas at `.ai/memory/` — one home, all tools.
These behaviors are projected into each tool's native instruction format.

## Session Startup (silent — do not output anything)

On every session start, read these files silently:
1. Read `.ai/memory/USER.md` (~1.4 KB max)
2. Read `.ai/memory/MEMORY.md` (~2.5 KB max, curated working scratchpad)
3. Read `.ai/memory/daily/{today’s date in YYYY-MM-DD}.md` if it exists
4. If today’s daily file has no prior sessions, also read yesterday's

These files are your "frozen snapshot" — loaded once at session start.
Mid-session writes persist to disk but take effect next session.
Total injected: ~3,000 tokens. Do not load more than this at startup.

## Memory Write

When the user says "remember this", "note that", "update memory", "save this",
or "forget about":
1. Read `.ai/memory/MEMORY.md` in full
2. Check for duplicates (scan for substring match)
3. Check character count of the file
4. If under 2,500 chars: append the new fact under the appropriate section
5. If over cap: consolidate — merge similar entries, remove stale ones, then add
6. Actions: add (append), replace (find substring + swap), remove (confirm with user first)
7. After writing: "Saved -- will be active from next session."

## Memory Budget

- `.ai/memory/MEMORY.md`: 2,500 character cap
- `.ai/memory/USER.md`: 1,375 character cap
- Before writing, always check file size first
- If over cap, consolidate existing entries before adding new ones

## Memory Retrieval

When the user asks about past context, conversations, or decisions:

1. **Tier 0**: Check `.ai/memory/MEMORY.md` and today’s daily log — already in context, zero cost
2. **Tier 1**: Read recent daily logs (`.ai/memory/daily/` files from past few days)
3. **Tier 2 — Wiki & Topics (LOCAL, in this repo)**: The Atlas **wiki** (`wiki/`,
   configured by `.ai/wiki.yaml`) and **topics** (`topics/`, configured by
   `.ai/topics.yaml`) are durable, version-controlled knowledge that lives **inside
   this repo** — they are NOT external services. Search them with the CLI:
   - Wiki: `ai-atlas wiki query "<query>"` (then read the matched page under `wiki/pages/`)
   - Topics: `ai-atlas topic list` / `ai-atlas topic open <slug>`
   When the user says "the wiki", "the Atlas wiki", or "in the wiki", this is the
   only thing they mean. **Never** reach for M365 Copilot, GitHub Copilot Spaces, or
   any external/web tool to answer it — those are wrong by default for Atlas
   wiki/topic questions.
4. **Tier 3**: If MemSearch is installed, run `memsearch search "query" --top-k 5`
5. **Tier 4**: If MemSearch is installed, run `memsearch expand <chunk_hash>` for full context
6. **Fallback**: "I don’t have a record of that."

Only escalate if the previous tier didn’t find the answer. If the user explicitly
names the wiki or a topic, go straight to Tier 2.

## Daily Log

Track session activity in `.ai/memory/daily/{YYYY-MM-DD}.md`:

``markdown
#### Session N
**Goal**: (one line -- what the user wanted to accomplish)
**Deliverables**: (files created or modified)
**Decisions**: (key decisions and rationale)
**Open threads**: (anything left unfinished)
``

Log these silently as they happen. Never announce "I’ve logged that."

## Promotion Triggers (memory → topic → wiki)

Knowledge flows through three stages. Watch for these signals and **nudge the
user proactively, but only at session end** — batched into one line, never
mid-task. The user can act or ignore. This is judgment, not automation.

The ladder:
- **MEMORY.md** — short-lived scratchpad: active threads, pending decisions.
- **Topic** (`ai-atlas topic`) — the desk: a subject spanning sessions, with its
  own notes and files.
- **Wiki** (`ai-atlas wiki`) — durable, reusable knowledge that won't churn.

Nudge when:

| Signal | Suggest |
|--------|---------|
| A thread in `Active Threads` recurs across **≥2 sessions** | open a **topic** for it |
| A subject gathers **≥3 related notes/files**, or is actively worked | open a **topic** workspace |
| A topic is marked **`done`**, or a fact has stayed stable / been referenced repeatedly | **promote to wiki** |
| `MEMORY.md` nears the **2,500-char cap** | offload durable items to wiki, prune the rest |

Rules:
1. Surface candidates **at session end only**, in a single line (e.g.
   "Promotion candidates: EZ Rates thread → topic; ai-os engine path → wiki").
2. Never interrupt active work to raise a promotion.
3. Suggest, don't act — wait for the user to confirm before creating topics or
   wiki pages.
4. If there are no candidates, say nothing.

## Session End

Before ending a session:
1. Update today’s daily log with deliverables, decisions, and open threads
2. If significant new facts were learned, add them to `.ai/memory/MEMORY.md`
3. Check Promotion Triggers — surface any candidates in one line (see above)
4. If `.ai/memory/MEMORY.md` is over cap, consolidate