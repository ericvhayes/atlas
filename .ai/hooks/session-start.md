# Hook: Session Start

Universal behavior for the session start hook. The projector converts this
into each tool's native hook format.

## Trigger
- **Copilot**: `sessionStart` event
- **Opencode**: `session.created` event (via plugin)
- **Claude**: `SessionStart` event

## Behavior

On session start:
1. Ensure `.ai/memory/` directory structure exists:
   - `.ai/memory/daily/`
   - `.ai/memory/transcripts/`
2. Create `.ai/memory/MEMORY.md` from template if it doesn't exist
3. Create `.ai/memory/USER.md` from template if it doesn't exist
4. Output the contents of `.ai/memory/MEMORY.md` and `.ai/memory/USER.md`
   to be injected into the session context

## Implementation Notes

- For **Copilot** (command hook): stdout from the hook is added as `additionalContext`
- For **Opencode** (plugin function): use `event` hook on `session.created`,
  read files and inject via `chat.params` hook
- For **Claude** (command hook): stdout is added to context on `SessionStart`

## Project Detection

- Check whether the current working directory has a `.ai/` folder.
- If `.ai/` exists, use project-level instructions and overrides from this project.
- If `.ai/` does not exist and this is not the Atlas source repo, suggest:
  `ai-atlas init --project`
- Project mode creates `.ai/instructions.md` for local overrides while inheriting
  Atlas global defaults.
