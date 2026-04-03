# Shoptera Product Intelligence — Gemini Integration

Search product catalogs across 2500+ Central European e-shops. No authentication required.

## Tools

### search_products

Semantic product search using natural language. Understands intent, synonyms, and multilingual queries.

**Endpoint:** `GET https://api.shoptera.ai/api/v1/search`

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

**Example:**

```
GET https://api.shoptera.ai/api/v1/search?q=dárek+pro+zahradníka&max_price=500&currency=CZK&origin_country=CZ
```

---

### search_products_by_text

Keyword search in product titles. Exact token matching, deterministic results.

**Endpoint:** `GET https://api.shoptera.ai/api/v1/search/text`

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

**Example:**

```
GET https://api.shoptera.ai/api/v1/search/text?title=Nike+Air+Max&brand=Nike&origin_country=CZ
```

---

### lookup_by_gtin

GTIN/EAN/UPC barcode lookup (8-14 digits).

**Endpoint:** `GET https://api.shoptera.ai/api/v1/search/gtin/{gtin}`

**Parameters:**
- `gtin` (path, required) — barcode number (8-14 digits)
- `origin_country` (string) — 2-letter ISO code
- `target_country` (string) — 2-letter ISO code
- `limit` (integer) — 1-50, default 10

**Example:**

```
GET https://api.shoptera.ai/api/v1/search/gtin/5901234123457?origin_country=CZ
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

## When to Use Which Tool

| User Intent | Tool |
|------------|------|
| Describes what they want | `search_products` |
| Names a specific product | `search_products_by_text` |
| Has a barcode number | `lookup_by_gtin` |

## Rate Limit

300 requests per hour per IP. Shared across all endpoints.

## MCP

MCP endpoint: `https://api.shoptera.ai/mcp` (streamable HTTP)

The same three tools are available via MCP protocol.
