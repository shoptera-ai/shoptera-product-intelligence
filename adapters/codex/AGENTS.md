# Shoptera Product Intelligence

## Capabilities

- Semantic product search across 2500+ Central European e-shops (~8.5M products)
- Keyword search by product title and brand
- GTIN/EAN barcode lookup across all indexed e-shops
- Add-to-cart actions for supported platforms
- Coverage: CZ, SK, PL, HU, RO, DE, AT

## API

Base URL: `https://api.shoptera.ai`

No authentication required. Rate limit: 300 requests/hour per IP.

### Semantic Search

Natural language product search. Understands intent, synonyms, and multilingual queries.

```bash
curl "https://api.shoptera.ai/api/v1/search?q=d%C3%A1rek+pro+zahradn%C3%ADka&max_price=500&currency=CZK&origin_country=CZ&limit=5"
```

Parameters: `q` (required), `origin_country`, `target_country`, `min_price`, `max_price`, `currency`, `brand`, `category`, `availability`, `eshop_domain`, `limit` (1-50).

### Keyword Search

Exact token matching in product titles. Faster, deterministic.

```bash
curl "https://api.shoptera.ai/api/v1/search/text?title=Nike+Air+Max+90&brand=Nike&origin_country=CZ"
```

Parameters: `title` (required), `brand`, `category`, `origin_country`, `target_country`, `min_price`, `max_price`, `currency`, `limit` (1-50).

### GTIN/EAN Lookup

Find products by barcode number (8-14 digits).

```bash
curl "https://api.shoptera.ai/api/v1/search/gtin/5901234123457?origin_country=CZ"
```

Parameters: `origin_country`, `target_country`, `limit` (1-50).

### Response Format

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
      "image_url": "https://...",
      "product_url": "https://...",
      "availability": "in_stock",
      "eshop_name": "Shop Name",
      "eshop_domain": "shop.cz",
      "origin_country": "CZ",
      "target_countries": ["CZ", "SK"],
      "score": 0.87,
      "cart_action": {
        "action": "add_to_cart",
        "url": "https://...",
        "method": "GET",
        "button_text": null
      }
    }
  ],
  "total_found": 1
}
```

`score` is only present in semantic search results.

## When to Use

- **Semantic search**: User describes what they want ("gift for a gardener", "comfortable running shoes")
- **Keyword search**: User knows the product name ("Nike Air Max 90", "stříbrný přívěsek")
- **GTIN lookup**: User has a barcode number

## Cart Actions

Each product has a `cart_action.method`:

- `GET` — navigate to `url` to add to cart automatically
- `browser_click` — navigate to `url`, click button matching `button_text`
- `view_product` — show `url` to the user

## MCP

MCP endpoint: `https://api.shoptera.ai/mcp` (streamable HTTP, stateless)

Tools: `search_products`, `search_products_by_text`, `lookup_by_gtin`
