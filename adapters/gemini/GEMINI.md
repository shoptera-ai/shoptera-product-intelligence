# Shoptera Product Intelligence — Gemini Integration

Search product catalogs across 2500+ Central European e-shops. No authentication required.

## Tools

### search_products

Semantic product search using natural language. Understands intent, synonyms, and multilingual queries.

**Endpoint:** `GET https://shoptera.ai/api/v1/search`

**Parameters:**
- `q` (string, required) — natural language query
- `origin_country` (string) — 2-letter ISO code (CZ, SK, PL, HU, RO, DE, AT)
- `target_country` (string) — 2-letter ISO code
- `min_price` (number) — minimum price
- `max_price` (number) — maximum price
- `currency` (string) — 3-letter ISO code (CZK, EUR, PLN, HUF, RON)
- `brand` (string) — exact brand (case-sensitive)
- `category` (string) — category keyword
- `availability` (string) — in_stock, out_of_stock, preorder
- `eshop_domain` (string) — specific e-shop domain
- `limit` (integer) — 1-50, default 10
- `fields` (string) — comma-separated list of fields to return (reduces response size by up to 70%)

**Example:**

```
GET https://shoptera.ai/api/v1/search?q=dárek+pro+zahradníka&max_price=500&currency=CZK&origin_country=CZ
```

---

### search_products_by_text

Keyword search in product titles. Exact token matching, deterministic results.

**Endpoint:** `GET https://shoptera.ai/api/v1/search/text`

**Parameters:**
- `title` (string, required) — keywords to match in titles
- `brand` (string) — exact brand
- `category` (string) — category keyword
- `origin_country` (string) — 2-letter ISO code
- `target_country` (string) — 2-letter ISO code
- `min_price` (number) — minimum price
- `max_price` (number) — maximum price
- `currency` (string) — 3-letter ISO code
- `limit` (integer) — 1-50, default 10
- `fields` (string) — comma-separated list of fields to return (reduces response size by up to 70%)

**Example:**

```
GET https://shoptera.ai/api/v1/search/text?title=Nike+Air+Max&brand=Nike&origin_country=CZ
```

---

### lookup_by_gtin

GTIN/EAN/UPC barcode lookup (8-14 digits).

**Endpoint:** `GET https://shoptera.ai/api/v1/search/gtin/{gtin}`

**Parameters:**
- `gtin` (path, required) — barcode number (8-14 digits)
- `origin_country` (string) — 2-letter ISO code
- `target_country` (string) — 2-letter ISO code
- `limit` (integer) — 1-50, default 10
- `fields` (string) — comma-separated list of fields to return (reduces response size by up to 70%)

**Example:**

```
GET https://shoptera.ai/api/v1/search/gtin/5901234123457?origin_country=CZ
```

---

## Response Format

All search endpoints return:

```json
{
  "results": [
    {
      "title": "Product Name",
      "description": "Product description",
      "price": 499.0,
      "currency": "CZK",
      "brand": "Brand",
      "category": "Category > Subcategory",
      "gtin": "5901234123457",
      "image_url": "https://cdn.example.cz/product.jpg",
      "product_url": "https://example.cz/product",
      "availability": "in_stock",
      "eshop_name": "Example Shop",
      "eshop_domain": "example.cz",
      "origin_country": "CZ",
      "target_countries": ["CZ", "SK"],
      "score": 0.87,
      "cart_action": {
        "action": "add_to_cart",
        "url": "https://example.cz/cart/add?id=123",
        "method": "GET",
        "button_text": null
      }
    }
  ],
  "total_found": 1
}
```

`score` is only present in semantic search results (0-1, higher = more relevant).

## Cart Actions

Each product has `cart_action.method`:

- `GET` — navigate to `url` to add to cart automatically
- `browser_click` — navigate to `url`, click button matching `button_text`
- `view_product` — show `url` to the user

**Available fields for `fields` parameter:** `title`, `description`, `price`, `currency`, `brand`, `category`, `gtin`, `image_url`, `product_url`, `availability`, `eshop_name`, `eshop_domain`, `origin_country`, `target_countries`, `score` (semantic only), `cart_action`

> **Saving tokens:** Use `fields` to request only the data you need. For price comparisons, `fields=title,price,product_url` is often sufficient. Always include `cart_action` if you need add-to-cart functionality.

## When to Use Which Tool

| User Intent | Tool |
|------------|------|
| Describes what they want | `search_products` |
| Names a specific product | `search_products_by_text` |
| Has a barcode number | `lookup_by_gtin` |

## Rate Limit

300 requests per hour per IP. Shared across all endpoints.

## MCP

MCP endpoint: `https://shoptera.ai/mcp/` (streamable HTTP)

The same three tools are available via MCP protocol.

---

## Merchant Feed Optimizer

For e-shop owners: authenticated MCP server for AI-driven product feed optimization.

**MCP endpoint:** `https://shoptera.ai/mcp/merchant/` (requires `Authorization: Bearer shopt_YOUR_KEY`)

Get your API key from [Shoptera Settings](https://shoptera.ai/settings) → API Keys.

### Merchant Tools

| Tool | Description |
|------|-------------|
| `list_my_projects` | Discover shops you own or are a team member of |
| `get_status` | Feed overview, pending counts per workflow, recommended action |
| `get_workflow` | Instructions and validation rules for a workflow |
| `get_products` | Fetch products with filters and pagination |
| `get_priority_products` | Products ranked by optimization impact score |
| `save_results` | Stage optimization results for merchant review |
| `find_product_sources` | Web search guidance and CSS selectors for enrichment |

### Workflows

title, description, gtin, enrichment, condition, age_gender, bundle, duplicate, rich_text

See [Merchant Workflows](../../capabilities/merchant-workflows.md) and [Merchant API Reference](../../api/merchant-reference.md) for details.
