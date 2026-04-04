# Changelog

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
