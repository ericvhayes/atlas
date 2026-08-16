# Atlas v2 Setup — Orientation

Atlas v2 is Eric Hayes's personal AI knowledge system, rebuilt clean on
2026-08-16 around a three-store architecture: a git repository holds the text
control plane (identity, notes, configuration), a OneDrive folder called
atlas_data holds bulk documents, and a second OneDrive folder called
atlas_lite holds this generated projection for Microsoft 365 Copilot. This
topic records how the system was bootstrapped, how this machine is bound to
its storage, and how the pieces are operated day to day.

## Atlas v2 Setup — what it is

The atlas repository lives at `~/Documents/AI_Brain/atlas` and is pushed to
`github.com/ericvhayes/atlas`. It was started blank on 2026-08-16 (a
deliberate decision — no migration from the previous atlas), carrying forward
only the identity layer: Atlas's soul and voice, Eric's role and preferences,
behavioral rules, and hook specifications. The engine that organizes and
projects the atlas is **axis**, a Python CLI maintained in the agora monorepo
(`agora/engines/axis`) and installed on this machine as an editable uv tool,
so `axis` and `axis-mcp` are on PATH everywhere.

## Atlas v2 Setup — current state

- Storage bindings on the Mac: data root `~/OneDrive/AI_Brain/atlas_data`,
  lite root `~/OneDrive/AI_Brain/atlas_lite`, both recorded per-machine in
  `~/.axis-data.yaml` under the atlas id `atlas-personal`.
- atlas_data uses the standard layout: `Inbox/` (capture drop zone),
  `Topics/<slug>/sources|out`, `Library/`, `Archive/`.
- The working flow is live end to end: files dropped anywhere in
  `Topics/<slug>/` are discovered and recorded in git-side manifests by
  `axis data sync`; Inbox items are filed by `axis data ingest` (filename
  hints like "topic-slug - name.pdf" propose the destination); semantic
  search covers document contents (pptx/docx/xlsx/pdf text) via a local
  ollama embedding model; session hooks reconcile everything automatically
  at session start and end.
- Atlas Lite (this bundle) regenerates via `axis lite export` — a
  reconciling export that only writes changed files and only publishes
  content explicitly approved for M365.

## Atlas v2 Setup — key decisions

- **2026-08-14 — three-store split decided** (recorded in the previous
  atlas's design topic): git for the text control plane, OneDrive
  atlas_data as the authoritative home for documents, OneDrive atlas_lite
  as a generated one-way projection. OneDrive is the preferred location for
  sensitive material; publication to Lite is default-deny per item.
- **2026-08-14 — blank restart confirmed** over carrying the old content
  forward; the previous atlas remains as an archive.
- **2026-08-14 — manifests use mtime/size only** (no hashing), so
  reconciliation never forces OneDrive to download placeholder files.
- **2026-08-14 — consumer lite profile** for this personal atlas: browsable
  files plus paste-ready Copilot instructions, no declarative-agent
  manifest until consumer Copilot's folder grounding is verified.
- **2026-08-16 — commit is the approval gate**: lite export refuses to run
  while any published source file has uncommitted changes.

## Atlas v2 Setup — open questions

- Verify what the personal (outlook.com) Microsoft Copilot can actually
  ground on — folder scoping via Agent Builder is unconfirmed; the
  paste-ready instructions file is the interim path.
- The work machine (Windows, UPS tenant) is not yet bootstrapped; it will
  bind a separate work atlas to the UPS OneDrive.
- Scheduled unattended runs (daily sync/export) await the argus control
  plane; today the session hooks and manual runs cover it.

## Atlas v2 Setup — glossary

- **axis** — the CLI engine that organizes the atlas and generates
  per-tool configuration (Claude Code, GitHub Copilot, OpenCode).
- **atlas_data** — the OneDrive folder holding bulk documents; the
  authoritative store for binary files.
- **atlas_lite** — this folder: a generated, read-only projection of atlas
  knowledge for M365 Copilot grounding.
- **manifest** — a small git-tracked file per topic listing the data files
  that live in OneDrive (name, size, modified time).
- **orientation pack** — a self-contained topic briefing (this document)
  written for chunk-based retrieval.
- **argus** — Eric's desktop cockpit app; will schedule and trigger axis
  operations as the control plane.
