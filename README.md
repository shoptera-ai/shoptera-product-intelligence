# Shoptera Product Intelligence

Search product catalogs across thousands of Central European e-shops. Semantic search, keyword matching, GTIN/EAN lookup — via REST API or MCP.

**~2,500 e-shops** | **~8.5M products** | **7 countries** (CZ, SK, PL, HU, RO, DE, AT)

Live stats: [`GET /stats/global`](https://shoptera.ai/api/stats/global)

---

## Quickstart

No authentication required. Base URL: `https://shoptera.ai/api`

**Semantic search** — natural language, understands intent:

```bash
curl "https://shoptera.ai/api/v1/search?q=dárek+pro+zahradníka+do+500+Kč&max_price=500&currency=CZK&origin_country=CZ"
```

**Keyword search** — exact title matching, fast:

```bash
curl "https://shoptera.ai/api/v1/search/text?title=Nike+Air+Max+90&brand=Nike"
```

**GTIN/EAN lookup** — find e-shops by barcode:

```bash
curl "https://shoptera.ai/api/v1/search/gtin/5901234123457"
```

**Saving tokens:** Add `fields` to return only what you need (up to 70% smaller response):

```bash
curl "https://shoptera.ai/api/v1/search?q=boty&limit=5&fields=title,price,product_url,cart_action"
```

---

## Quick Install

### Claude Code (one command)

```bash
claude mcp add --transport http shoptera https://shoptera.ai/api/mcp
```

### Cursor

Add to Cursor Settings → Features → MCP → Add New MCP Server, or edit `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "shoptera": { "url": "https://shoptera.ai/api/mcp" }
  }
}
```

### Windsurf

Add via Cascade → MCP Servers → Add Server, or edit `~/.codeium/windsurf/mcp_config.json`:

```json
{
  "mcpServers": {
    "shoptera": { "url": "https://shoptera.ai/api/mcp" }
  }
}
```

### VS Code (Copilot / Continue)

Edit `.vscode/mcp.json` in your workspace:

```json
{
  "mcpServers": {
    "shoptera": { "url": "https://shoptera.ai/api/mcp" }
  }
}
```

### Any tool (universal installer)

```bash
npx add-mcp https://shoptera.ai/api/mcp -n shoptera -g -y
```

---

## All Platforms

| Platform | Setup | Details |
|----------|-------|---------|
| **Claude Code** | `claude mcp add --transport http shoptera https://shoptera.ai/api/mcp` | [Skill guide](adapters/claude-code/SKILL.md) |
| **Cursor** | [MCP config](adapters/cursor/mcp-config.json) | Settings → Features → MCP |
| **Windsurf** | [MCP config](adapters/windsurf/mcp-config.json) | Cascade → MCP Servers |
| **VS Code** | [MCP config](adapters/claude-code/mcp-config.json) | `.vscode/mcp.json` |
| **OpenAI Codex** | [AGENTS.md](adapters/codex/AGENTS.md) | Agent config reference |
| **ChatGPT** | [OpenAPI spec](adapters/chatgpt/openapi.yaml) | Custom GPT actions. [Instructions](adapters/chatgpt/instructions.md) |
| **Gemini** | [GEMINI.md](adapters/gemini/GEMINI.md) | Tool definitions and endpoints |
| **Any HTTP client** | [Examples](adapters/generic/http-examples.md) | curl, Python, JavaScript |

### MCP Details

Endpoint: `https://shoptera.ai/api/mcp` (streamable HTTP, stateless, no auth)

3 tools: `search_products`, `search_products_by_text`, `lookup_by_gtin`

---

## Capabilities

- [**Product Search**](capabilities/product-search.md) — semantic vs keyword vs GTIN, when to use which, filters, scoring
- [**Cart Actions**](capabilities/cart-actions.md) — three action types, how to handle each
- [**Data Coverage**](capabilities/data-coverage.md) — countries, data freshness, live stats

---

## API Reference

Full documentation: [`api/reference.md`](api/reference.md)

OpenAPI spec: [`api/openapi.yaml`](api/openapi.yaml)

### Endpoints

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/api/v1/search?q=...` | Semantic search (natural language) |
| `GET` | `/api/v1/search/text?title=...` | Keyword search (exact title match) |
| `GET` | `/api/v1/search/gtin/{gtin}` | GTIN/EAN barcode lookup |
| `GET` | `/stats/global` | Catalog statistics |

### Code Examples

- [Semantic search](api/examples/semantic-search.md) — curl, Python, JavaScript
- [Keyword search](api/examples/keyword-search.md) — curl, Python, JavaScript
- [GTIN/EAN lookup](api/examples/gtin-ean-lookup.md) — curl, Python, JavaScript
- [Cart actions](api/examples/cart-actions.md) — handling all three action types

---

## Rate Limits

**300 requests per hour** per IP address. Shared across REST API and MCP. No authentication needed.

429 responses include a `Retry-After` header.

---

## Contributing

Found an issue with the docs? Open a PR:

1. Fork this repo
2. Edit the relevant file
3. Submit a pull request

Please keep changes consistent with [`api/reference.md`](api/reference.md) as the source of truth for API behavior.

---

## License

MIT
