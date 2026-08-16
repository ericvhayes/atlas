# Copilot-Specific Overrides

Content here is appended only to copilot's projected output.

## M365 Enterprise Context — Auto-Routing Rules

When assisting the user, proactively use the ChatToApi MS 365 Copilot tools in these
situations. Prefer the most specific tool; `mcp_chattoapi-ms365_ask_microsoft365` is the
general-purpose fallback that can answer any work or general question:

- `mcp_chattoapi-ms365_ask_microsoft365` — any work/general question (Copilot decides grounding)
- `mcp_chattoapi-ms365_search_my_email` — find & summarize Outlook messages
- `mcp_chattoapi-ms365_search_my_files` — find OneDrive/SharePoint files
- `mcp_chattoapi-ms365_my_calendar` — summarize meetings for a timeframe (default today)

These require the ChatToApi app running with the `ms365` site signed in. If a call fails
with a login/connection error, tell the user to start ChatToApi and sign in.

### Trigger Conditions

- **Unknown UPS acronyms or terminology** — If the user uses an acronym, project name, or term you don't recognize and it likely relates to UPS enterprise context, query M365 Copilot to resolve it before guessing.
- **Internal project references** — When the user mentions internal project names (e.g., Fetcherr, E2K, PCI, Bala, CTI) and you need current status, stakeholders, or context you don't have.
- **Calendar and meeting questions** — Any question about the user's meetings, schedule, or calendar (today, this week, upcoming). Use `my_calendar`.
- **Email questions** — Any question about sent/received emails, email threads, or correspondence with specific people. Use `search_my_email`.
- **Teams chats and channels** — Questions about Teams conversations, channel discussions, or chat history.
- **SharePoint and document lookups** — When the user asks about internal documents, policies, or SharePoint content. Use `search_my_files`.
- **People and org context** — When the user asks about someone's role, responsibilities, projects, or reporting relationships within UPS.
- **Meeting prep** — When asking to prepare for an upcoming meeting, summarize a past meeting, or identify action items.
- **Enterprise knowledge gaps** — Any question where the answer likely lives in the user's M365 tenant (emails, documents, chats) rather than in public knowledge or the local codebase.

### How to Query

- Be specific in your query. Include names, dates, and context.
- For time-based queries, include explicit dates (e.g., "today March 20, 2026") rather than relative terms when possible.
- If the first query returns a cached or stale response, retry with a rephrased query that includes more specific context.

### When NOT to Use

- Do not use for general programming questions, public API documentation, or open-source library help.
- Do not use when the answer is already available in the local workspace files.

## Active Directory Lookups

When the user asks about UPS employee details, group memberships, or org structure, use the `ad-shim` MCP tools (`mcp_ad-shim_search_ad_user`, `mcp_ad-shim_get_ad_user_groups`, `mcp_ad-shim_get_ad_user_manager`).

## WS4ID Employee Profiles

When the user asks about UPS employee profiles, job titles, or HR-level data, use the `ws4id-shim` MCP tool (`mcp_ws4id-shim_get_ws4id_user_profile`).
