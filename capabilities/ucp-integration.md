# UCP Integration

Shoptera provides per-e-shop MCP endpoints that allow e-shops to declare Shoptera as their product search provider via the [Universal Commerce Protocol](https://ucp.dev/).

## How It Works

1. E-shop deploys a `/.well-known/ucp` file on their domain
2. The file declares Shoptera's per-e-shop MCP endpoint as a service
3. AI agents discover the file and connect to Shoptera for product search
4. Search results are automatically scoped to that e-shop's products

## Per-E-shop Endpoint

```
https://shoptera.ai/mcp/shops/{domain}/
```

Example: `https://shoptera.ai/mcp/shops/muj-eshop.cz/`

This is a standard MCP endpoint (streamable HTTP) that exposes UCP Catalog tools scoped to a single e-shop.

### UCP Tools

| Tool | Description |
|------|-------------|
| `search_catalog` | Semantic product search using natural language |
| `lookup_catalog` | Keyword matching in product titles |
| `get_product` | GTIN/EAN/UPC barcode lookup |

These are standard [UCP Catalog capability](https://ucp.dev/draft/specification/catalog/) tools. Results include `metadata.cart_url` for add-to-cart redirect on supported platforms.

### Cart Actions

For supported platforms, each product result includes a `metadata` object:

```json
{
  "metadata": {
    "cart_url": "https://muj-eshop.cz/action/Cart/addItem?id=12345&quantity=1",
    "cart_method": "redirect",
    "platform": "shoptet"
  }
}
```

Currently supported platforms for cart redirect:
- **Shoptet** — direct add-to-cart URL

For unsupported platforms, `cart_url` falls back to the product page URL.

## UCP Profile Structure

The `/.well-known/ucp` file deployed on the e-shop's domain should follow this structure:

```json
{
  "ucp": {
    "version": "2026-01-23",
    "services": {
      "dev.ucp.shopping": [
        {
          "version": "draft",
          "spec": "https://ucp.dev/draft/specification/overview",
          "transport": "mcp",
          "endpoint": "https://shoptera.ai/mcp/shops/{your-domain}/",
          "schema": "https://ucp.dev/draft/services/shopping/mcp.openrpc.json"
        }
      ]
    },
    "capabilities": {
      "dev.ucp.shopping.catalog.search": [
        {
          "version": "draft",
          "spec": "https://ucp.dev/draft/specification/catalog/",
          "schema": "https://ucp.dev/draft/schemas/shopping/catalog-search.json"
        }
      ]
    },
    "payment_handlers": {}
  }
}
```

If the e-shop already has a `/.well-known/ucp` file, Shoptera's service and capabilities are appended to the existing profile without overwriting any existing entries.

## Global vs Per-E-shop

| Endpoint | Scope | Use Case |
|----------|-------|----------|
| `https://shoptera.ai/mcp/` | All ~8.5M products | AI agents searching across the full catalog |
| `https://shoptera.ai/mcp/shops/{domain}/` | One e-shop | AI agents that discovered the e-shop via UCP |

See [adapters/ucp/profile-example.json](../adapters/ucp/profile-example.json) for a complete example profile.
