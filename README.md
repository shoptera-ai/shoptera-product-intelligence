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
| | | |
| **Merchant MCP** | `shoptera-merchant` at `https://shoptera.ai/mcp/merchant/` | [Requires API key](#merchant-feed-optimizer) |

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

## Privacy Policy

Shoptera Product Intelligence is a **read-only product search service**. We do not require authentication, do not collect personal data, and do not process payments.

- **No personal data collected** — no accounts, no cookies, no tracking pixels
- **Query logging** — we log anonymized search queries (without IP addresses) for service improvement and aggregate usage statistics
- **No data sharing** — query logs are not shared with third parties
- **Rate limiting** — IP addresses are used transiently for rate limiting (300 req/hour) and are not stored
- **Product data** — all product information is sourced from publicly available e-shop feeds
- **Cart actions** — purchase redirects go directly to the original e-shop; Shoptera does not intermediate transactions

For questions, contact hello@shoptera.ai.

---

## Agent Discovery

This server supports multiple discovery protocols for AI agents:

| Protocol | Endpoint | Description |
|----------|----------|-------------|
| **MCP Server Card** | [`/.well-known/mcp/server-card.json`](https://shoptera.ai/.well-known/mcp/server-card.json) | MCP auto-discovery (tools, capabilities, transport) |
| **A2A Agent Card** | [`/.well-known/agent-card.json`](https://shoptera.ai/.well-known/agent-card.json) | Agent-to-Agent protocol discovery (skills, interfaces) |
| **UCP Manifest** | [`/.well-known/ucp`](https://shoptera.ai/.well-known/ucp) | Universal Commerce Protocol binding |

---

## Contributing

Found an issue with the docs? Open a PR:

1. Fork this repo
2. Edit the relevant file
3. Submit a pull request

Please keep changes consistent with [`api/reference.md`](api/reference.md) as the source of truth for API behavior.

---

## Merchant Feed Optimizer

**For e-shop owners:** Shoptera also provides an authenticated MCP server for optimizing your own product feeds with AI. Connect your AI assistant (Claude, GPT, Cursor, etc.) to your Shoptera account and let it improve titles, descriptions, GTINs, categories, and more — with results staged in an Inbox for your review before publishing.

**9 workflows** | **7 MCP tools** | **API key auth** (`shopt_...`)

MCP endpoint: `https://shoptera.ai/mcp/merchant/` (streamable HTTP, requires Bearer token)

### Tools

| Tool | Description |
|------|-------------|
| `list_my_projects` | Discover all shops you own or are a team member of |
| `get_status` | Feed overview: total products, optimization progress, pending by workflow |
| `get_workflow` | Instructions and validation rules for a specific workflow |
| `get_products` | Fetch products with filters (missing fields, price range, category) |
| `get_priority_products` | Cross-workflow ranking by impact score |
| `save_results` | Stage optimization results in Inbox for merchant review |
| `find_product_sources` | Search guidance and CSS selectors for web enrichment |

### Workflows

| Workflow | What It Optimizes |
|----------|-------------------|
| `title` | Product titles for CTR and search visibility |
| `description` | Product descriptions with attributes |
| `gtin` | Missing GTIN/EAN codes via web search |
| `enrichment` | Category, tags, FAQs, highlights, use cases |
| `condition` | Product condition (new, refurbished, used) |
| `age_gender` | Gender and age group for apparel |
| `bundle` | Multipack detection and count |
| `duplicate` | Duplicate product detection |
| `rich_text` | HTML descriptions for Facebook/Allegro |

### Installation

Get your API key from [Shoptera Settings](https://shoptera.ai/settings) → API Keys.

**Claude Code:**

```bash
claude mcp add --transport http shoptera-merchant https://shoptera.ai/mcp/merchant/ --header "Authorization: Bearer shopt_YOUR_KEY"
```

**Cursor / Windsurf / VS Code:**

```json
{
  "mcpServers": {
    "shoptera-merchant": {
      "url": "https://shoptera.ai/mcp/merchant/",
      "headers": {
        "Authorization": "Bearer shopt_YOUR_KEY"
      }
    }
  }
}
```

> **Note:** Register as `shoptera-merchant`, not `shoptera` — keep the public product search server separate.

### Documentation

- [**Merchant Workflows**](capabilities/merchant-workflows.md) — all 9 workflows, fields, validation rules, confidence tiers
- [**Merchant API Reference**](api/merchant-reference.md) — full tool signatures, parameters, response schemas
- Adapter-specific setup: [Claude Code](adapters/claude-code/merchant-mcp-config.json) · [Cursor](adapters/cursor/merchant-mcp-config.json) · [Windsurf](adapters/windsurf/merchant-mcp-config.json)

---

## UCP Integration

E-shops indexed by Shoptera can deploy a [UCP](https://ucp.dev/) profile (`/.well-known/ucp`) to enable AI agent discovery. Shoptera provides per-e-shop MCP endpoints:

```
https://shoptera.ai/mcp/shops/{your-domain}/
```

This exposes UCP-standard Catalog tools (`search_catalog`, `lookup_catalog`, `get_product`) scoped to your e-shop's products. Results include `metadata.cart_url` for add-to-cart redirect on supported platforms (Shoptet).

See [capabilities/ucp-integration.md](capabilities/ucp-integration.md) for full details and [adapters/ucp/profile-example.json](adapters/ucp/profile-example.json) for an example profile.

---

## License

MIT
