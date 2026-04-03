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

## Platform Setup

| Platform | Config | Instructions |
|----------|--------|--------------|
| **Claude Code** | [`adapters/claude-code/mcp-config.json`](adapters/claude-code/mcp-config.json) | Copy to your Claude Code MCP settings. [Skill guide](adapters/claude-code/SKILL.md) |
| **Cursor** | [`adapters/cursor/mcp-config.json`](adapters/cursor/mcp-config.json) | Add to Cursor MCP settings |
| **Windsurf** | [`adapters/windsurf/mcp-config.json`](adapters/windsurf/mcp-config.json) | Add to Windsurf MCP settings |
| **OpenAI Codex** | [`adapters/codex/AGENTS.md`](adapters/codex/AGENTS.md) | Reference in your agent config |
| **ChatGPT (Custom GPT)** | [`adapters/chatgpt/openapi.yaml`](adapters/chatgpt/openapi.yaml) | Import as custom actions. [Instructions](adapters/chatgpt/instructions.md) |
| **Gemini** | [`adapters/gemini/GEMINI.md`](adapters/gemini/GEMINI.md) | Tool definitions and endpoints |
| **Any HTTP client** | [`adapters/generic/http-examples.md`](adapters/generic/http-examples.md) | curl, Python, JavaScript examples |

### MCP Quick Setup

MCP endpoint: `https://shoptera.ai/api/mcp` (streamable HTTP, stateless)

```json
{
  "mcpServers": {
    "shoptera": {
      "type": "streamable-http",
      "url": "https://shoptera.ai/api/mcp"
    }
  }
}
```

3 MCP tools: `search_products`, `search_products_by_text`, `lookup_by_gtin`

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
