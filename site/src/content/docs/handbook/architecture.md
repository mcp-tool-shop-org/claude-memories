---
title: Architecture
description: How claude-memories fits into the Knowledge OS stack.
sidebar:
  order: 3
---

## Knowledge OS layers

claude-memories is a **Layer 2 adapter** in the Knowledge OS stack:

| Layer | Package | Role |
|-------|---------|------|
| Kernel | `@mcptoolshop/ai-loadout` | Routing types, matching, validation, hierarchical resolution |
| Adapter | `@mcptoolshop/claude-rules` | CLAUDE.md optimization — converts rule files to dispatch tables |
| Adapter | `@mcptoolshop/claude-memories` | MEMORY.md optimization — converts memory files to dispatch tables |

Same kernel, different document types. Both adapters produce compatible `LoadoutIndex` dispatch tables that the kernel can route against.

## How it works

1. **Parse** — reads MEMORY.md and finds all topic references (arrow format: `Name → path`)
2. **Read** — loads each referenced topic file from disk
3. **Extract** — pulls keywords from topic names, file headings, and optional frontmatter
4. **Index** — generates a `LoadoutIndex` with one entry per topic, keyed by extracted keywords
5. **Validate** — checks structural integrity (missing files, orphans, duplicates)

## The dispatch table

The output `index.json` is a standard `LoadoutIndex` from ai-loadout:

```json
{
  "version": "1.0",
  "budget": { "maxTokens": 43127 },
  "entries": [
    {
      "id": "ai-loadout",
      "name": "AI Loadout",
      "path": "memory/ai-loadout.md",
      "keywords": ["loadout", "routing", "dispatch", "kernel"],
      "priority": "domain",
      "tokens": 1850
    }
  ]
}
```

An agent receives a task, the kernel matches task keywords against entry keywords, and only the matching topic files get loaded into context.

## Design constraints

- **Local-only** — no network calls, no telemetry, no external services
- **Read-mostly** — only writes `index.json`; never modifies MEMORY.md or topic files
- **Deterministic** — same inputs always produce the same outputs
- **Zero runtime dependencies** — built on ai-loadout, which itself has zero dependencies
