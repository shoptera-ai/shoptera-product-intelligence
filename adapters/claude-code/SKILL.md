---
name: shoptera-product-search
description: Search products across Central European e-shops. Use when user asks about products, prices, shopping, or needs product data.
---

# Shoptera Product Search

## Install

```bash
claude mcp add --transport http shoptera https://shoptera.ai/mcp/
```

You have access to the Shoptera product catalog — over 8 million products from 2500+ e-shops across Czech Republic, Slovakia, Poland, Hungary, Romania, Germany, and Austria.

## When to Use

Use this skill whenever the user:
- Asks about product availability, prices, or where to buy something
- Wants to compare products across e-shops
- Needs product data (titles, descriptions, GTINs, images)
- Mentions a barcode, EAN, or GTIN number
- Asks for gift ideas or product recommendations

## MCP Tools Available

### `search_products` — Semantic Search
Use for open-ended queries, natural language, intent-based search.

```
search_products(q="dárek pro zahradníka do 500 Kč", origin_country="CZ", max_price=500, currency="CZK")
```

### `search_products_by_text` — Keyword Search
Use when you know the exact product name or model number.

```
search_products_by_text(title="Nike Air Max 90", brand="Nike", origin_country="CZ")
```

### `lookup_by_gtin` — GTIN/EAN Lookup
Use when the user provides a barcode number (8-14 digits).

```
lookup_by_gtin(gtin="5901234123457", origin_country="CZ")
```

## Saving Tokens

All three tools accept an optional `fields` parameter (list of strings) to return only specific fields. This reduces response size by up to 70%.

```
search_products(q="running shoes", limit=5, fields=["title", "price", "product_url", "cart_action"])
```

For price comparisons, `fields=["title", "price", "product_url"]` is often sufficient. Always include `"cart_action"` if you need add-to-cart functionality.

**Available fields:** `title`, `description`, `price`, `currency`, `brand`, `category`, `gtin`, `image_url`, `product_url`, `availability`, `eshop_name`, `eshop_domain`, `origin_country`, `target_countries`, `score` (semantic only), `cart_action`

## Choosing the Right Tool

```
User has a barcode number? → lookup_by_gtin
User knows the exact product name? → search_products_by_text
User describes what they want? → search_products
```

## Interpreting Results

Each result includes:
- `title`, `description`, `price`, `currency` — product details
- `brand`, `category`, `gtin` — classification
- `image_url` — show this to the user when possible
- `product_url` — always provide this link so the user can visit the product
- `availability` — in_stock, out_of_stock, or preorder
- `eshop_name`, `eshop_domain` — which e-shop sells it
- `origin_country`, `target_countries` — where the e-shop operates
- `score` — relevance (0-1, only in semantic search). Above 0.80 is a strong match.
- `cart_action` — how to add the product to cart

## Handling Cart Actions

Every product has a `cart_action` with a `method` field:

- **`GET`**: The `url` adds the product to cart when visited. You can provide this link directly: "Click here to add to cart: {url}"
- **`browser_click`**: The user needs to visit `url` and click the button labeled `button_text`. Tell them: "Visit {url} and click '{button_text}' to add to cart."
- **`view_product`**: Just show the `url` — "Visit the product page: {url}"

## REST API Fallback

If MCP tools are unavailable, use the REST API directly:

```bash
# Semantic search
curl "https://shoptera.ai/api/v1/search?q=query&origin_country=CZ"

# Keyword search
curl "https://shoptera.ai/api/v1/search/text?title=product+name"

# GTIN lookup
curl "https://shoptera.ai/api/v1/search/gtin/5901234123457"
```

## Presenting Results

When showing products to the user:
1. Show the product image (if available) and title
2. Include price and currency
3. Mention the e-shop name
4. Always include `product_url` as a clickable link
5. If the user wants to buy, use the cart action
6. For price comparisons, search the same product across multiple e-shops

## Reference

- [Product Search capabilities](../../capabilities/product-search.md)
- [Cart Actions guide](../../capabilities/cart-actions.md)
- [API Reference](../../api/reference.md)
