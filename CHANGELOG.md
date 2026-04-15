# Changelog

## 1.2.0 — 2026-04-15

- Added Merchant Feed Optimizer documentation (authenticated MCP for e-shop owners)
- Added `capabilities/merchant-workflows.md` — 9 workflows, confidence tiers, batch processing
- Added `api/merchant-reference.md` — full tool signatures, parameters, response schemas
- Added merchant MCP configs for Claude Code, Cursor, Windsurf adapters
- Updated Codex, Gemini, ChatGPT, and generic adapters with merchant sections
- Updated Claude Code SKILL.md with merchant tools and install instructions
- Updated README with Merchant Feed Optimizer section and installation guide

## 1.1.0 — 2026-04-04

- Added `smithery.yaml` for Smithery registry one-click install
- Added `pyproject.toml` with full package metadata
- Added MCP prompts: `product_search_guide`, `price_comparison`
- Added MCP resources: `shoptera://catalog/stats`, `shoptera://search/guide`
- Added tool annotations (`readOnlyHint`, `idempotentHint`, `openWorldHint`)
- Added parameter-level descriptions via pydantic `Field()` for all tool parameters
- Restructured README with dedicated Tools, Installation, and Usage sections

## 1.0.0 — 2026-04-03

- Initial release
- REST API: semantic search, keyword search, GTIN/EAN lookup
- MCP server: streamable HTTP protocol
- Adapters: Claude Code, Codex, Cursor, Windsurf, ChatGPT, Gemini
- Data: 2500+ e-shops across CZ, SK, PL, HU, RO, DE, AT
