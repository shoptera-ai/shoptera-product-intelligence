# API Reference

Base URL: `https://shoptera.ai/api`

Authentication: **None required** — all endpoints are public.

Rate limit: **300 requests per hour** per IP address (shared across REST and MCP).

---

## Semantic Search

Search products using natural language. Uses vector embeddings to understand intent, synonyms, and context.

```
GET /api/v1/search
```

### Parameters

| Name | Type | Required | Description | Example |
|------|------|----------|-------------|---------|
| `q` | string | **Yes** | Natural language search query | `dárek pro zahradníka do 500 Kč` |
| `origin_country` | string | No | 2-letter ISO country code of the e-shop | `CZ` |
| `target_country` | string | No | 2-letter ISO country code of the target market | `SK` |
| `min_price` | number | No | Minimum price (>= 0) | `100` |
| `max_price` | number | No | Maximum price (>= 0) | `500` |
| `currency` | string | No | 3-letter ISO currency code | `CZK` |
| `brand` | string | No | Exact brand name (case-sensitive) | `Nike` |
| `category` | string | No | Category keyword match | `Obuv` |
| `availability` | string | No | One of: `in_stock`, `out_of_stock`, `preorder` | `in_stock` |
| `eshop_domain` | string | No | Filter by e-shop domain | `fantasyobchod.cz` |
| `limit` | integer | No | Number of results (1-50, default 10) | `10` |
| `fields` | string | No | Comma-separated list of fields to return. Default: all fields. | `title,price,product_url` |

> **Saving tokens:** Use the `fields` parameter to reduce response size by up to 70%. For comparison/price queries, `fields=title,price,product_url` is often sufficient. Always include `cart_action` if you need add-to-cart functionality.

### Example Request

```bash
curl "https://shoptera.ai/api/v1/search?q=d%C3%A1rek+pro+zahradn%C3%ADka&max_price=500&currency=CZK&origin_country=CZ"
```

### Example Response

```json
{
  "results": [
    {
      "title": "Zahradnické nůžky Fiskars",
      "description": "Profesionální zahradnické nůžky s ergonomickou rukojetí.",
      "price": 389.0,
      "currency": "CZK",
      "brand": "Fiskars",
      "category": "Zahrada > Nářadí",
      "gtin": "6411501984210",
      "image_url": "https://cdn.zahradnictvi.cz/fiskars-nuzky.jpg",
      "product_url": "https://zahradnictvi.cz/fiskars-nuzky-profesionalni",
      "availability": "in_stock",
      "eshop_name": "Zahradnictví.cz",
      "eshop_domain": "zahradnictvi.cz",
      "origin_country": "CZ",
      "target_countries": ["CZ", "SK"],
      "score": 0.9123,
      "cart_action": {
        "action": "add_to_cart",
        "url": "https://zahradnictvi.cz/cart/add?id=98765",
        "method": "GET",
        "button_text": null
      }
    }
  ],
  "total_found": 1
}
```

---

## Keyword Search

Search products by exact keyword matching in titles. Deterministic results, faster than semantic search.

```
GET /api/v1/search/text
```

### Parameters

| Name | Type | Required | Description | Example |
|------|------|----------|-------------|---------|
| `title` | string | **Yes** | Keyword search in product titles | `stříbrný přívěsek` |
| `brand` | string | No | Exact brand name (case-sensitive) | `Fantasy Šperky` |
| `category` | string | No | Category keyword match | `Šperky` |
| `origin_country` | string | No | 2-letter ISO country code of the e-shop | `CZ` |
| `target_country` | string | No | 2-letter ISO country code of the target market | `SK` |
| `min_price` | number | No | Minimum price (>= 0) | `500` |
| `max_price` | number | No | Maximum price (>= 0) | `2000` |
| `currency` | string | No | 3-letter ISO currency code | `CZK` |
| `limit` | integer | No | Number of results (1-50, default 10) | `10` |
| `fields` | string | No | Comma-separated list of fields to return. Default: all fields. | `title,price,product_url` |

### Example Request

```bash
curl "https://shoptera.ai/api/v1/search/text?title=st%C5%99%C3%ADbrn%C3%BD+p%C5%99%C3%ADv%C4%9Bsek&origin_country=CZ"
```

### Example Response

```json
{
  "results": [
    {
      "title": "Stříbrný přívěsek s ametystem",
      "description": "Elegantní stříbrný přívěsek Ag 925 s broušeným ametystem.",
      "price": 1290.0,
      "currency": "CZK",
      "brand": "Fantasy Šperky",
      "category": "Šperky > Přívěsky",
      "gtin": "8590123456789",
      "image_url": "https://cdn.fantasyobchod.cz/privesek-ametyst.jpg",
      "product_url": "https://fantasyobchod.cz/stribrny-privesek-ametyst",
      "availability": "in_stock",
      "eshop_name": "Fantasy Obchod",
      "eshop_domain": "fantasyobchod.cz",
      "origin_country": "CZ",
      "target_countries": ["CZ"],
      "cart_action": {
        "action": "add_to_cart",
        "url": "https://fantasyobchod.cz/stribrny-privesek-ametyst",
        "method": "browser_click",
        "button_text": "Přidat do košíku"
      }
    }
  ],
  "total_found": 1
}
```

> **Note:** Keyword search results do not include a `score` field.

---

## GTIN/EAN Lookup

Look up products by GTIN, EAN, or UPC barcode number.

```
GET /api/v1/search/gtin/{gtin}
```

### Path Parameters

| Name | Type | Required | Description | Example |
|------|------|----------|-------------|---------|
| `gtin` | string | **Yes** | GTIN/EAN/UPC barcode (8-14 digits) | `5901234123457` |

### Query Parameters

| Name | Type | Required | Description | Example |
|------|------|----------|-------------|---------|
| `origin_country` | string | No | 2-letter ISO country code | `CZ` |
| `target_country` | string | No | 2-letter ISO country code | `SK` |
| `limit` | integer | No | Number of results (1-50, default 10) | `10` |
| `fields` | string | No | Comma-separated list of fields to return. Default: all fields. | `title,price,product_url` |

### Example Request

```bash
curl "https://shoptera.ai/api/v1/search/gtin/5901234123457?origin_country=CZ"
```

### Example Response

```json
{
  "results": [
    {
      "title": "Nike Air Max 90",
      "description": "Comfortable running shoes with Air Max cushioning.",
      "price": 2499.0,
      "currency": "CZK",
      "brand": "Nike",
      "category": "Obuv > Běžecké boty",
      "gtin": "5901234123457",
      "image_url": "https://cdn.sportshop.cz/nike-air-max.jpg",
      "product_url": "https://sportshop.cz/nike-air-max-90",
      "availability": "in_stock",
      "eshop_name": "SportShop",
      "eshop_domain": "sportshop.cz",
      "origin_country": "CZ",
      "target_countries": ["CZ", "SK"],
      "cart_action": {
        "action": "add_to_cart",
        "url": "https://sportshop.cz/cart/add?id=12345",
        "method": "GET",
        "button_text": null
      }
    }
  ],
  "total_found": 1
}
```

> **Note:** GTIN lookup results do not include a `score` field.

---

## Global Stats

Public statistics about the Shoptera catalog. Cached for 1 hour.

```
GET /stats/global
```

### Parameters

None.

### Example Request

```bash
curl "https://shoptera.ai/api/stats/global"
```

### Example Response

```json
{
  "mcp": {
    "total_impressions": 12500,
    "unique_queries": 3200,
    "agents_connected": 15
  },
  "catalog": {
    "eshops_count": 2500,
    "feeds_count": 3100,
    "products_count": 8500000,
    "by_country": [
      { "country": "CZ", "label": "Czech Republic", "count": 1800 },
      { "country": "SK", "label": "Slovakia", "count": 350 },
      { "country": "PL", "label": "Poland", "count": 150 },
      { "country": "HU", "label": "Hungary", "count": 80 },
      { "country": "RO", "label": "Romania", "count": 60 },
      { "country": "DE", "label": "Germany", "count": 40 },
      { "country": "AT", "label": "Austria", "count": 20 }
    ]
  }
}
```

---

## Response Schema

All three search endpoints return the same response structure:

| Field | Type | Description |
|-------|------|-------------|
| `results` | array | List of matching products |
| `total_found` | integer | Total number of matches |

### Product Object

| Field | Type | Description |
|-------|------|-------------|
| `title` | string | Product title |
| `description` | string | Product description |
| `price` | number | Product price |
| `currency` | string | 3-letter ISO currency code |
| `brand` | string | Brand name |
| `category` | string | Product category path |
| `gtin` | string | GTIN/EAN/UPC barcode |
| `image_url` | string | Product image URL |
| `product_url` | string | Product page URL |
| `availability` | string | One of: `in_stock`, `out_of_stock`, `preorder` |
| `eshop_name` | string | E-shop display name |
| `eshop_domain` | string | E-shop domain |
| `origin_country` | string | Country where the e-shop is based |
| `target_countries` | array | Countries the e-shop ships to |
| `score` | number | Semantic relevance score (0-1). **Only present in semantic search.** |
| `cart_action` | object | Cart action details (see below) |

### Cart Action Object

| Field | Type | Description |
|-------|------|-------------|
| `action` | string | Always `add_to_cart` |
| `url` | string | URL for the cart action |
| `method` | string | One of: `GET`, `browser_click`, `view_product` |
| `button_text` | string or null | Button text to click (only for `browser_click`) |

See [Cart Actions](../capabilities/cart-actions.md) for detailed handling instructions.

---

## Error Responses

### 422 — Validation Error

Returned when required parameters are missing or have invalid format.

```json
{
  "detail": [
    {
      "loc": ["query", "q"],
      "msg": "field required",
      "type": "value_error.missing"
    }
  ]
}
```

### 429 — Rate Limit Exceeded

Returned when the 300 requests/hour limit is exceeded. Includes a `Retry-After` header with seconds until the limit resets.

```
HTTP/1.1 429 Too Many Requests
Retry-After: 120
Content-Type: application/json

{
  "detail": "Rate limit exceeded. Try again in 120 seconds."
}
```

---

## Token Saving

Use the `fields` parameter on any search endpoint to request only the fields you need. This can reduce response size by up to 70%.

**Available fields:** `title`, `description`, `price`, `currency`, `brand`, `category`, `gtin`, `image_url`, `product_url`, `availability`, `eshop_name`, `eshop_domain`, `origin_country`, `target_countries`, `score` (semantic only), `cart_action`

**Examples:**

| Use Case | Recommended Fields |
|----------|--------------------|
| Price comparison | `title,price,currency,product_url,eshop_name` |
| Product listing | `title,price,currency,image_url,product_url` |
| Shopping with cart | `title,price,product_url,cart_action` |
| Full details | omit `fields` (returns everything) |

```bash
# Only fetch title, price, and link — ~70% smaller response
curl "https://shoptera.ai/api/v1/search?q=boty&limit=5&fields=title,price,product_url"
```

---

## Rate Limiting

- **Limit:** 300 requests per hour per IP address
- **Scope:** Shared across REST API and MCP protocol
- **Header:** `Retry-After` included in 429 responses
- **Tip:** Cache results on your side for repeated queries
