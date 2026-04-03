# Product Search

Shoptera offers three search modes, each suited to different use cases. All three are available via both REST API and MCP protocol.

---

## Semantic Search

**Endpoint:** `GET /api/v1/search?q=...`
**MCP tool:** `search_products`

Uses vector embeddings (Voyage AI, 1024 dimensions) to understand the meaning behind a query. Handles synonyms, intent, and multilingual input.

**Strengths:**
- Understands natural language: "gift for a gardener under 500 CZK" works
- Cross-language: Czech, Slovak, German, English queries all work
- Finds products even when exact keywords don't match
- Returns a `score` field (0-1) ranking results by semantic relevance

**Limitations:**
- Slightly slower than keyword search (vector similarity computation)
- May surface loosely related products at lower scores

**Best for:** Open-ended queries, gift ideas, category browsing, intent-based search.

---

## Keyword Search

**Endpoint:** `GET /api/v1/search/text?title=...`
**MCP tool:** `search_products_by_text`

Exact token matching against product titles. Deterministic — same query always returns the same results.

**Strengths:**
- Fast and predictable
- Exact title matching — no guessing
- Works well with model numbers, specific product names

**Limitations:**
- No synonym handling: "running shoes" won't match "běžecké boty"
- Order-independent but requires exact tokens

**Best for:** Known product names, model numbers, brand + product combinations.

---

## GTIN/EAN Lookup

**Endpoint:** `GET /api/v1/search/gtin/{gtin}`
**MCP tool:** `lookup_by_gtin`

Exact barcode match. Finds all e-shops selling a product with the given GTIN/EAN/UPC.

**Strengths:**
- Exact match — no ambiguity
- Compare prices across e-shops for the same product
- Supports EAN-8, EAN-13, UPC-A, GTIN-14

**Limitations:**
- Not all products have GTINs
- Returns nothing if the barcode isn't in the catalog

**Best for:** Price comparison, product identification from barcodes, inventory cross-referencing.

---

## Decision Tree

```
User has a barcode/EAN number?
  └─ Yes → GTIN/EAN Lookup
  └─ No
      ├─ User knows the exact product name or model?
      │   └─ Yes → Keyword Search
      └─ No → Semantic Search
```

More specifically:

| Scenario | Search Type |
|----------|-------------|
| "Find me a gift for a gardener" | Semantic |
| "Nike Air Max 90" | Keyword |
| "EAN 5901234123457" | GTIN |
| "Best running shoes under 2000 CZK" | Semantic |
| "stříbrný přívěsek s ametystem" | Keyword or Semantic |
| "Compare prices for this barcode" | GTIN |
| "Something for a birthday party" | Semantic |

---

## Available Filters

All search types support country filters. Semantic and keyword search support additional filters:

| Filter | Semantic | Keyword | GTIN | Description |
|--------|:--------:|:-------:|:----:|-------------|
| `origin_country` | Yes | Yes | Yes | E-shop's country (2-letter ISO) |
| `target_country` | Yes | Yes | Yes | Market the e-shop ships to |
| `brand` | Yes | Yes | No | Exact brand name (case-sensitive) |
| `category` | Yes | Yes | No | Category keyword match |
| `min_price` | Yes | Yes | No | Minimum price |
| `max_price` | Yes | Yes | No | Maximum price |
| `currency` | Yes | Yes | No | Currency for price filters |
| `availability` | Yes | No | No | in_stock, out_of_stock, preorder |
| `eshop_domain` | Yes | No | No | Specific e-shop domain |
| `limit` | Yes | Yes | Yes | Results count (1-50, default 10) |

---

## Scoring

Only semantic search returns a `score` field (0 to 1):

- **0.90+** — Very strong match. Query and product are closely related.
- **0.75-0.90** — Good match. Relevant but may not be an exact fit.
- **0.60-0.75** — Moderate match. Loosely related.
- **Below 0.60** — Weak match. Usually not relevant.

Keyword and GTIN searches do not include a score.

---

## Rate Limits

All search endpoints share the same rate limit: **300 requests per hour per IP address**. This limit is shared between REST API and MCP protocol calls.
