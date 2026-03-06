# Changelog

## 1.0.0 — 2026-03-06

Initial release.

- `parseMemoryMd()` — parse MEMORY.md structure and references
- `analyzeMemoryMd()` — analyze topic files, detect orphans/missing
- `generateIndex()` — dispatch table from MEMORY.md + topic files
- `validateMemory()` — lint memory files for structural issues
- `generateStats()` / `formatStats()` — token budget dashboard
- `extractKeywords()` — keyword extraction from names and content
- CLI commands: `analyze`, `index`, `validate`, `stats`
- Multi-base path resolution (handles MEMORY.md inside memory/ dir)
- Lazy loading support (`--lazy` flag)
- Built on `@mcptoolshop/ai-loadout` v1.1.0
- Zero external CLI dependencies
- 31 tests
