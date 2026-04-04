# Shoptera Product Intelligence

Search product catalogs across thousands of Central European e-shops. Semantic search, keyword matching, GTIN/EAN lookup — via REST API or MCP.

**~2,500 e-shops** | **~8.5M products** | **7 countries** (CZ, SK, PL, HU, RO, DE, AT)

Live stats: [`GET /stats/global`](https://shoptera.ai/api/stats/global)

---

## Tools

Three MCP tools are available. All are read-only and require no authentication.

### `search_products` — Semantic Search

Natural language search using vector embeddings. Understands intent, synonyms, and context across Czech, Slovak, German, Polish, Hungarian, Romanian, and English.

**When to use:** Open-ended queries, gift ideas, category browsing, intent-based search.

**Returns:** Products ranked by semantic relevance score (0-1). Includes title, description, price, currency, brand, category, gtin, image_url, product_url, availability, eshop info, and cart_action.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `query` | string | Yes | Natural language search query |
| `origin_country` | string | No | E-shop country: CZ, SK, PL, HU, RO, DE, AT |
| `target_country` | string | No | Target market filter |
| `min_price` | number | No | Minimum price (set `currency` too) |
| `max_price` | number | No | Maximum price (set `currency` too) |
| `currency` | string | No | ISO 4217: CZK, EUR, PLN, HUF, RON |
| `brand` | string | No | Exact brand name (case-sensitive) |
| `category` | string | No | Category keyword match |
| `availability` | string | No | `in_stock`, `out_of_stock`, `preorder` |
| `eshop_domain` | string | No | Filter by e-shop domain |
| `limit` | integer | No | Results count, 1-50 (default 10) |
| `fields` | list | No | Fields to include (saves up to 70% tokens) |

### `search_products_by_text` — Keyword Search

Exact keyword matching in product titles. Deterministic results, faster than semantic search.

**When to use:** Known product names, model numbers, brand + product combinations.

**Returns:** Products matching all keywords. Same fields as semantic search (without score).

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `title` | string | Yes | Keywords for product title (AND logic) |
| `brand` | string | No | Exact brand name (case-sensitive) |
| `category` | string | No | Category keyword match |
| `origin_country` | string | No | E-shop country: CZ, SK, PL, HU, RO, DE, AT |
| `target_country` | string | No | Target market filter |
| `min_price` | number | No | Minimum price (set `currency` too) |
| `max_price` | number | No | Maximum price (set `currency` too) |
| `currency` | string | No | ISO 4217: CZK, EUR, PLN, HUF, RON |
| `limit` | integer | No | Results count, 1-50 (default 10) |
| `fields` | list | No | Fields to include (saves up to 70% tokens) |

### `lookup_by_gtin` — GTIN/EAN Barcode Lookup

Exact barcode match. Finds all e-shops selling a product by GTIN/EAN/UPC.

**When to use:** Price comparison by barcode, product identification.

**Returns:** All products matching the barcode. Same fields as keyword search.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `gtin` | string | Yes | GTIN/EAN/UPC barcode (8-14 digits) |
| `origin_country` | string | No | E-shop country: CZ, SK, PL, HU, RO, DE, AT |
| `target_country` | string | No | Target market filter |
| `limit` | integer | No | Results count, 1-50 (default 10) |
| `fields` | list | No | Fields to include (saves up to 70% tokens) |

### Tool Selection Guide

```
User has a barcode number?  -->  lookup_by_gtin
User knows the exact product name?  -->  search_products_by_text
User describes what they want?  -->  search_products
```

---

## Installation

No authentication required. MCP endpoint: `https://shoptera.ai/mcp/` (streamable HTTP, stateless)

### Claude Code

```bash
claude mcp add --transport http shoptera https://shoptera.ai/mcp/
```

### Cursor

Add to Cursor Settings > Features > MCP > Add New MCP Server, or edit `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "shoptera": { "url": "https://shoptera.ai/mcp/" }
  }
}
```

### Windsurf

Add via Cascade > MCP Servers > Add Server, or edit `~/.codeium/windsurf/mcp_config.json`:

```json
{
  "mcpServers": {
    "shoptera": { "url": "https://shoptera.ai/mcp/" }
  }
}
```

### VS Code (Copilot / Continue)

Edit `.vscode/mcp.json` in your workspace:

```json
{
  "mcpServers": {
    "shoptera": { "url": "https://shoptera.ai/mcp/" }
  }
}
```

### Any tool (universal installer)

```bash
npx add-mcp https://shoptera.ai/mcp/ -n shoptera -g -y
```

### All Platforms

| Platform | Setup | Details |
|----------|-------|---------|
| **Claude Code** | `claude mcp add --transport http shoptera https://shoptera.ai/mcp/` | [Skill guide](adapters/claude-code/SKILL.md) |
| **Cursor** | [MCP config](adapters/cursor/mcp-config.json) | Settings > Features > MCP |
| **Windsurf** | [MCP config](adapters/windsurf/mcp-config.json) | Cascade > MCP Servers |
| **VS Code** | [MCP config](adapters/claude-code/mcp-config.json) | `.vscode/mcp.json` |
| **OpenAI Codex** | [AGENTS.md](adapters/codex/AGENTS.md) | Agent config reference |
| **ChatGPT** | [OpenAPI spec](adapters/chatgpt/openapi.yaml) | Custom GPT actions. [Instructions](adapters/chatgpt/instructions.md) |
| **Gemini** | [GEMINI.md](adapters/gemini/GEMINI.md) | Tool definitions and endpoints |
| **Any HTTP client** | [Examples](adapters/generic/http-examples.md) | curl, Python, JavaScript |

---

## Usage

### Semantic search — natural language, understands intent

```bash
curl "https://shoptera.ai/api/v1/search?q=dárek+pro+zahradníka+do+500+Kč&max_price=500&currency=CZK&origin_country=CZ"
```

### Keyword search — exact title matching, fast

```bash
curl "https://shoptera.ai/api/v1/search/text?title=Nike+Air+Max+90&brand=Nike"
```

### GTIN/EAN lookup — find e-shops by barcode

```bash
curl "https://shoptera.ai/api/v1/search/gtin/5901234123457"
```

### Saving tokens — return only the fields you need

```bash
curl "https://shoptera.ai/api/v1/search?q=boty&limit=5&fields=title,price,product_url,cart_action"
```

### Cart actions

Every product includes a `cart_action` object:

- **`method: "GET"`** — navigate to `url` to add the product to cart automatically
- **`method: "browser_click"`** — navigate to `url`, then click the button matching `button_text`
- **`method: "view_product"`** — show the product page URL to the user

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
