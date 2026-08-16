# Hook: Transcript Capture

Universal behavior for capturing session transcripts. The projector converts
this into each tool's native hook format.

## Trigger
- **Copilot**: `postToolUse` or `agentStop` event
- **Opencode**: `tool.execute.after` event (via plugin)
- **Claude**: `Stop` event

## Behavior

After significant agent output:
1. Get today's date in YYYY-MM-DD format
2. Ensure `.ai/memory/transcripts/` directory exists
3. Take the first 500 characters of the response
4. Append a timestamped entry to `.ai/memory/transcripts/{today}.md`:

```markdown
## {HH:MM:SS}
{first 500 chars of response, with excessive newlines collapsed}
```

## Implementation Notes

- Fire-and-forget — errors should never break the session
- For **Copilot**: command hook with bash/PowerShell script
- For **Opencode**: TypeScript plugin function
- For **Claude**: Node.js script executed by command hook
- Transcript files are gitignored (ephemeral, can be large)
- MemSearch indexes these for vector search retrieval
