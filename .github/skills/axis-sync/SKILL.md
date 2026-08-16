---
name: axis-sync
description: >
  Re-synchronize all AI tool configurations from the .ai/ source directory.
  Use when the user says "sync ai config", "update tool configs", or after
  editing files in .ai/.
allowed-tools: shell
---

## Steps

1. Run `axis sync` in the project root
2. Report what files were updated
3. If axis is not installed, suggest: `pip install axis`
