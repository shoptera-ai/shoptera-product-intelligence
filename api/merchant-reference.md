# Merchant API Reference

MCP endpoint: `https://shoptera.ai/mcp/merchant/` (streamable HTTP)

REST API base: `https://shoptera.ai/api/merchant`

Authentication: **Bearer API key required** — `Authorization: Bearer shopt_YOUR_KEY`

Get your API key from [Shoptera Settings](https://shoptera.ai/settings) → API Keys.

---

## list_my_projects

Discover all shops the authenticated user owns or is a team member of. Call this first to get project slugs.

### Parameters

None.

### Example Response

```json
{
  "projects": [
    {
      "id": "a1b2c3d4-...",
      "slug": "my-eshop",
      "name": "My E-shop",
      "source_url": "https://my-eshop.cz/feed.xml",
      "total_products": 1250,
      "last_sync": "2026-04-15T08:30:00Z"
    }
  ],
  "count": 1
}
```

---

## get_status

Feed overview: total products, optimization progress, and pending counts per workflow.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `project` | string | **Yes** | Project slug or UUID |

### Example Response

```json
{
  "shop_name": "My E-shop",
  "source_url": "https://my-eshop.cz/feed.xml",
  "total_products": 1250,
  "optimized_products": 890,
  "pending_optimization": 360,
  "pending_by_workflow": {
    "gtin": 120,
    "title": 85,
    "description": 95,
    "enrichment": 200,
    "condition": 45,
    "age_gender": 30,
    "bundle": 15,
    "duplicate": 8,
    "rich_text": 0
  },
  "recommended_action": {
    "workflow": "gtin",
    "pending": 120,
    "impact_score": 360.0,
    "weight": 3.0,
    "why": "Highest impact: 120 products x weight 3.0"
  },
  "action_ranking": [
    {"workflow": "gtin", "pending": 120, "impact_score": 360.0},
    {"workflow": "title", "pending": 85, "impact_score": 170.0},
    {"workflow": "description", "pending": 95, "impact_score": 142.5}
  ],
  "last_sync": "2026-04-15T08:30:00Z",
  "available_workflows": ["title", "description", "gtin", "enrichment", "condition", "age_gender", "bundle", "duplicate"],
  "agents_available": ["title", "description", "gtin", "enrichment", "condition", "age_gender", "bundle", "duplicate"]
}
```

> **Stateless resume:** Call `get_status` again after any interruption — pending counts are the checkpoint.

---

## get_workflow

Retrieves structured instructions and validation rules for a specific workflow. Call before starting a workflow to get field requirements and format notes.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `project` | string | **Yes** | Project slug or UUID |
| `workflow_type` | string | **Yes** | One of: `title`, `description`, `gtin`, `enrichment`, `condition`, `age_gender`, `bundle`, `duplicate`, `rich_text` |

### Example Response

```json
{
  "workflow_type": "title",
  "name": "Title Optimization",
  "description": "Optimize product titles for CTR and search visibility",
  "context": {
    "shop_name": "My E-shop",
    "country": "CZ",
    "target_markets": ["CZ", "SK"],
    "total_products": 1250,
    "top_categories": ["Elektronika", "Domácnost", "Zahrada"],
    "export_formats": ["google_xml", "heureka_xml"]
  },
  "target_fields": ["title"],
  "steps": [
    "Fetch products with suboptimal titles",
    "Analyze title quality (length, attributes, structure)",
    "Generate improved titles",
    "Save results"
  ],
  "result_schema": {
    "product_id": "UUID",
    "fields": {"title": "Improved title string"},
    "confidence": "0-100",
    "reason": "Why this change improves the title"
  },
  "format_notes": [
    "Target length: 60-80 characters",
    "Must include: brand, product type, key differentiator",
    "No HTML or URLs",
    "No ALL CAPS"
  ]
}
```

---

## get_products

Fetch products from a project with pagination, filtering, and field selection.

### Parameters

| Name | Type | Required | Description | Example |
|------|------|----------|-------------|---------|
| `project` | string | **Yes** | Project slug or UUID | `my-eshop` |
| `limit` | integer | No | Results per page, 1-100 (default 50) | `50` |
| `offset` | integer | No | Pagination offset (default 0) | `100` |
| `fields` | string | No | Comma-separated fields to return | `id,title,gtin,category` |
| `filter` | string | No | JSON filter object (see below) | `{"missing": ["gtin"]}` |
| `category` | string | No | Category keyword filter | `Elektronika` |
| `brand` | string | No | Exact brand name | `Nike` |
| `ids` | string | No | Comma-separated product UUIDs (max 100) | `uuid1,uuid2` |
| `price_gte` | number | No | Minimum price | `100` |
| `price_lte` | number | No | Maximum price | `5000` |
| `category_contains` | string | No | Category substring match | `boty` |

### Filter Object

Pass as JSON string in the `filter` parameter:

```json
{
  "missing": ["gtin", "description"],
  "title_length_lt": 40,
  "description_length_lt": 100,
  "enrichment_missing": true,
  "brand": "Nike",
  "price_gte": 100,
  "price_lte": 5000,
  "category_contains": "Obuv"
}
```

All filter fields are optional. `missing` accepts: `gtin`, `title`, `description`, `image`, `category`, `brand`, `tags`, `enrichment`.

### Example Response

```json
{
  "products": [
    {
      "id": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
      "external_id": "SKU-12345",
      "title": "Běžecké boty Nike",
      "description": "Sportovní obuv pro běh.",
      "price": 2499.0,
      "currency": "CZK",
      "category": "Obuv > Běžecké boty",
      "gtin": null,
      "brand": "Nike",
      "image_url": "https://cdn.my-eshop.cz/nike-running.jpg",
      "product_url": "https://my-eshop.cz/nike-bezecke-boty",
      "tags": [{"key": "color", "value": "černá"}],
      "enriched_data": {
        "categories": {"google": "Apparel & Accessories > Shoes"},
        "faq": [],
        "highlights": [],
        "use_cases": []
      }
    }
  ],
  "count": 1,
  "offset": 0,
  "limit": 50,
  "has_more": false
}
```

---

## get_priority_products

Cross-workflow ranking: returns products sorted by total fixable damage across all workflows. Use this to find the products that benefit most from optimization.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `project` | string | **Yes** | Project slug or UUID |
| `limit` | integer | No | Results count, 1-100 (default 25) |

### Example Response

```json
{
  "products": [
    {
      "id": "f47ac10b-...",
      "external_id": "SKU-12345",
      "title": "Běžecké boty Nike",
      "brand": "Nike",
      "category": "Obuv",
      "image_url": "https://cdn.my-eshop.cz/nike.jpg",
      "impact_score": 8.3,
      "issue_count": 4,
      "issues": [
        {"workflow": "gtin", "weight": 3.0, "label": "Missing GTIN"},
        {"workflow": "title", "weight": 2.0, "label": "Short title"},
        {"workflow": "description", "weight": 1.5, "label": "Missing description"},
        {"workflow": "enrichment", "weight": 1.0, "label": "Missing tags"}
      ]
    }
  ],
  "count": 25,
  "limit": 25,
  "scoring_weights": {
    "gtin": 3.0,
    "image": 2.5,
    "title": 2.0,
    "description": 1.5,
    "enrichment": 1.0,
    "tags": 0.8
  }
}
```

---

## save_results

Stage optimization results as suggestions in the Inbox. The merchant reviews and approves/rejects them in the web UI.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `project` | string | **Yes** | Project slug or UUID |
| `workflow_type` | string | **Yes** | Workflow that produced these results |
| `results_json` | string | **Yes** | JSON array of result objects (see schema below) |
| `batch_id` | string | No | UUID for idempotent retries within one agent run |

### Result Schema

```json
[
  {
    "product_id": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
    "current_value": "Běžecké boty Nike",
    "fields": {
      "title": "Nike Air Max 90 - Pánské běžecké boty, černá, lehká konstrukce"
    },
    "confidence": 85,
    "reason": "Added brand, model, gender, color, and key feature. Length: 65 chars."
  }
]
```

### Field-Specific Formats

| Field | Format | Notes |
|-------|--------|-------|
| `title` | string | Max 150 chars, no HTML |
| `description` | string | 150-500 chars target, no HTML |
| `gtin` | string | 8/12/13/14 digits, must pass GS1 checksum |
| `category` | string or `{"google": "...", "heureka": "..."}` | Standard taxonomy |
| `tags` | `[{"key": "color\|material\|season", "value": "..."}]` | Max 20, only allowed keys |
| `faq` | `["Q: ...\nA: ..."]` or `[{"question": "...", "answer": "..."}]` | Max 15 |
| `highlights` | `["..."]` | Max 10, each <= 150 chars |
| `use_cases` | `["..."]` | Max 5 |
| `condition` | string | `new`, `refurbished`, `used`, `for_parts` |
| `gender` | string | `male`, `female`, `unisex` |
| `age_group` | string | `adult`, `kids`, `infant`, `toddler`, `newborn` |
| `is_bundle` | boolean | `true` or `false` |
| `multipack` | integer | Pack count (>= 2) |
| `rich_text_description` | string | HTML, allowed tags only, max 5000 chars |

### Example Response

```json
{
  "saved": 12,
  "auto_approved": 8,
  "pending_review": 4,
  "skipped_no_op": 0,
  "errors": [],
  "batch_id": "550e8400-e29b-41d4-a716-446655440000",
  "workflow_type": "title",
  "text_auto_approve": false
}
```

> **Auto-approval:** Non-text fields with confidence >= 90 are auto-approved. Text fields (`title`, `description`) require `text_auto_approve` opt-in from the merchant.

---

## find_product_sources

Returns search guidance and CSS selectors for web-based enrichment (GTIN lookup, description research).

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `project` | string | **Yes** | Project slug or UUID |
| `product_id` | string | No | Specific product UUID |
| `query` | string | No | Custom search query |
| `limit` | integer | No | Number of suggestions (default 5) |

### Example Response

```json
{
  "query": "Nike Air Max 90 GTIN EAN",
  "search_tip": "Search for the product name + 'GTIN' or 'EAN' on manufacturer websites or barcode databases.",
  "selectors": {
    "gtin": "span.ean, td:contains('EAN'), [itemprop='gtin13']",
    "title": "h1, [itemprop='name']",
    "brand": "[itemprop='brand'], .brand-name",
    "price": "[itemprop='price'], .product-price",
    "description": "[itemprop='description'], .product-description"
  }
}
```

---

## Error Responses

### 401 — Unauthorized

Invalid or missing API key.

```json
{
  "error": "No user context. Check the Bearer API key."
}
```

### 404 — Project Not Found

Project does not exist or user does not have access.

```json
{
  "error": "Project not found."
}
```

### 422 — Validation Error

Invalid parameters or result format.

```json
{
  "error": "Confidence must be between 0 and 100."
}
```

---

## Reference

- [Merchant Workflows](../capabilities/merchant-workflows.md) — workflow details, field formats, confidence tiers
- [Product Search API](reference.md) — public product search (no auth)
- [README](../README.md) — project overview
