# claude-memories

> MEMORY.md optimizer and dispatch table generator for Claude Code.

[![npm](https://img.shields.io/npm/v/@mcptoolshop/claude-memories)](https://www.npmjs.com/package/@mcptoolshop/claude-memories)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

Put your MEMORY.md on a diet. claude-memories analyzes your memory files, generates a machine-readable dispatch table, and shows you where your context budget goes.

## The Problem

Claude Code's auto-memory grows into a giant MEMORY.md that eats context window. Every session loads 40K+ tokens of memories — most irrelevant to the current task.

## The Solution

claude-memories indexes your memory files into a dispatch table. An agent can route to the right memory topic on demand instead of loading everything.

```
MEMORY.md (669 tokens)  →  dispatch table  →  topic files (42K tokens)
     always loaded            routing            loaded on match
```

**98% savings** on a real 31-topic memory workspace.

## Install

```bash
npm install -g @mcptoolshop/claude-memories
```

## Commands

### analyze

Analyze MEMORY.md structure, references, and token costs.

```bash
claude-memories analyze MEMORY.md
```

### index

Generate a dispatch table (index.json) from your memory files.

```bash
claude-memories index MEMORY.md
claude-memories index MEMORY.md --lazy
claude-memories index MEMORY.md --out .claude/memory-index.json
```

### validate

Lint memory files for structural issues.

```bash
claude-memories validate MEMORY.md
```

Checks for: missing topic files, orphan files, duplicate references, empty names.

### stats

Token budget dashboard.

```bash
claude-memories stats MEMORY.md
```

```
╔══════════════════════════════════════════╗
║        Memory Token Budget               ║
╚══════════════════════════════════════════╝

  Total tokens:       43,127
  MEMORY.md inline:   669
  Topic files:        42,458

  Entries:            31
  Always loaded:      669 tokens
  On-demand total:    42,458 tokens
  Avg task load:      1,370 tokens
  Savings (lazy):     98%
```

## How It Works

1. Parses MEMORY.md for topic references (arrow format: `Name → path`)
2. Reads each topic file, extracts keywords from headings and content
3. Generates a LoadoutIndex (dispatch table) compatible with ai-loadout
4. Validates structural integrity (missing files, orphans, duplicates)

### Reference Format

MEMORY.md entries follow this format:

```
Topic Name — description → `memory/topic-file.md`
```

Both bulleted and non-bulleted formats are supported:

```
- AI Loadout — routing core for agents → `memory/ai-loadout.md`
Claude Rules — CLAUDE.md optimizer → `memory/claude-rules.md`
```

### Frontmatter (Optional)

Topic files can include frontmatter for fine-grained control:

```markdown
---
id: ai-loadout
keywords: [loadout, routing, dispatch, kernel]
patterns: [knowledge_routing]
priority: domain
triggers:
  task: true
  plan: true
  edit: false
---

# AI Loadout
...
```

Without frontmatter, keywords are auto-extracted from the topic name and headings.

## Architecture

claude-memories is a **Layer 2 adapter** in the Knowledge OS stack:

| Layer | Package | Role |
|-------|---------|------|
| Kernel | `@mcptoolshop/ai-loadout` | Routing types, matching, validation |
| Adapter | `@mcptoolshop/claude-rules` | CLAUDE.md optimization |
| Adapter | `@mcptoolshop/claude-memories` | MEMORY.md optimization |

Same kernel, different document types. Both produce compatible dispatch tables.

## Security

- **Local-only**: No network calls, no telemetry
- **Read-mostly**: Only writes index.json; never modifies MEMORY.md
- **Deterministic**: Same inputs → same outputs

See [SECURITY.md](SECURITY.md) for threat model.

## License

MIT
