# Hook: Memory Write

Universal behavior for writing to the curated memory system.
This is expressed as both a hook behavior and a skill.

## Trigger
- User says: "remember this", "note that", "update memory", "save this", "forget about"
- Or: agent determines a fact is worth persisting

## Behavior

1. Read `.ai/memory/MEMORY.md` in full
2. Determine action: **add**, **replace**, or **remove**
3. **Dedup check**: scan for substring match — skip or update if exists
4. **Cap check**: check file size — if over 2,500 chars, consolidate before adding
5. Write the change under the appropriate section:
   - `## Active Threads` — current work, open questions
   - `## Environment Notes` — URLs, tool versions, configs
   - `## Pending Decisions` — open decisions, options
6. Confirm: "Saved — will be active from next session."

## Remove Action

For removal: **always confirm with the user before deleting**.
Show the entry to be removed and ask for explicit confirmation.

## Consolidation

When over cap:
1. Identify entries that can be merged (related topics)
2. Remove stale entries (resolved threads, outdated info)
3. Shorten verbose entries to single-line facts
4. Report: "Consolidated memory: {N}/2,500 chars ({percent}%)"
