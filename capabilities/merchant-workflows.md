# Merchant Workflows

Shoptera Merchant Feed Optimizer provides 9 AI-driven workflows that analyze and improve your product feed data. Each workflow targets specific fields and produces suggestions staged in your Inbox for review.

MCP endpoint: `https://shoptera.ai/mcp/merchant/` (requires API key)

---

## Workflow Overview

| Workflow | Target Fields | Description |
|----------|---------------|-------------|
| `title` | title | Optimize product titles for CTR and search visibility (60-80 chars target) |
| `description` | description | Generate/improve descriptions from feed attributes (150-500 chars) |
| `gtin` | gtin | Find and verify missing GTIN/EAN codes via web search |
| `enrichment` | category, tags, faq, highlights, use_cases | Add structured attributes for better discoverability |
| `condition` | condition | Detect product condition: `new`, `refurbished`, `used`, `for_parts` |
| `age_gender` | gender, age_group | Classify apparel by gender and age group |
| `bundle` | is_bundle, multipack | Detect multipacks and extract counts |
| `duplicate` | _(detection only)_ | Find duplicate products in feed |
| `rich_text` | rich_text_description | Convert descriptions to HTML for Facebook/Allegro export |

---

## How It Works

```
1. list_my_projects()          → discover your shops
2. get_status(project)         → see what needs optimization
3. get_workflow(project, type) → get instructions for a workflow
4. get_products(project, ...)  → fetch products to optimize
5. save_results(project, ...)  → stage results in Inbox
```

The merchant reviews and approves/rejects suggestions in the Shoptera web UI. Approved changes are applied to the feed and re-exported automatically.

---

## Impact Scoring

`get_priority_products` ranks products by total fixable damage across all workflows:

| Field | Weight | Reason |
|-------|--------|--------|
| gtin | 3.0 | Google Shopping / Heureka / Zbozi blocker |
| image | 2.5 | Comparison engine drop |
| title | 2.0 | CTR lever in search results |
| description | 1.5 | Long-tail SEO + conversion |
| enrichment | 1.0 | Structured filters + discoverability |
| tags | 0.8 | Subset of enrichment |

**Recommended:** Start with the highest-impact workflow from `get_status().recommended_action`, not alphabetically.

---

## Confidence Tiers

Results use categorical confidence, not raw floats:

| Tier | Range | Label | Behavior |
|------|-------|-------|----------|
| Verified | 90-100 | High confidence | Auto-approved (non-text fields) |
| Likely | 75-89 | Medium confidence | Staged for review |
| Weak | 55-74 | Low confidence | Staged with warning |
| Rejected | < 55 | Below threshold | Automatically rejected |

> **Note:** Text fields (`title`, `description`) require the merchant to opt-in to auto-approval via `text_auto_approve` setting.

---

## Workflow Details

### `title` — Title Optimization

Improves product titles for search visibility and click-through rate.

- **Target:** 60-80 characters
- **Skip if:** title scores >= 80, SKU-like pattern, variant product, < 2 attributes available
- **Rules:** No HTML/URLs, no ALL CAPS, must include key attributes (brand, type, key feature)

### `description` — Description Optimization

Generates or improves product descriptions using feed attributes.

- **Target:** 150-500 characters
- **Skip if:** description > 150 chars and scores >= 40, identical to title
- **Flag if:** HTML/URLs in description, food without attributes, variant products
- **Rules:** Generate from feed attributes only, no hallucinated features

### `gtin` — GTIN Verification

Finds missing GTIN/EAN codes through web search and database lookup.

- **Format:** 8, 12, 13, or 14 digits, must pass GS1 checksum
- **Rules:** Verify against product title and brand before saving

### `enrichment` — Attribute Enrichment

Adds structured data across 5 sub-fields:

| Sub-field | Format | Limits |
|-----------|--------|--------|
| `category` | `{"google": "...", "heureka": "..."}` | Standard taxonomy paths |
| `tags` | `[{"key": "color\|material\|season", "value": "..."}]` | Max 20 per product |
| `faq` | `["Q: ...\nA: ..."]` or `[{"question": "...", "answer": "..."}]` | Max 15 per product |
| `highlights` | `["..."]` | Max 10, each <= 150 chars |
| `use_cases` | `["..."]` | Max 5 per product |

**Allowed tag keys:** `color`, `material`, `season` only.

### `condition` — Condition Detection

Detects product condition from title/description patterns.

- **Values:** `new`, `refurbished`, `used`, `for_parts`
- **Skip categories:** food, digital products, gift cards
- **Detection:** Regex patterns first (refurbished/used/outlet), LLM fallback

### `age_gender` — Age & Gender Classification

Classifies apparel products by gender and age group.

- **Gender values:** `male`, `female`, `unisex`
- **Age group values:** `adult`, `kids`, `infant`, `toddler`, `newborn`
- **Skip if:** non-apparel category, variant product, already classified
- **Returns:** Up to 2 suggestions (one per field)

### `bundle` — Bundle Detection

Detects multipacks and extracts pack counts.

- **Fields:** `is_bundle` (boolean) + `multipack` (integer count)
- **Detection:** Numeric regex (3ks, pack of 3) → text patterns → LLM
- **Returns:** 1-2 suggestions

### `duplicate` — Duplicate Detection

Identifies duplicate products within the feed. Detection-only — does not modify product data. Results link to the suspected duplicate.

### `rich_text` — Rich Text Description

Converts plain-text descriptions to structured HTML for Facebook Catalog and Allegro export.

- **Allowed HTML tags:** `<p>`, `<ul>`, `<ol>`, `<li>`, `<strong>`, `<b>`, `<em>`, `<i>`, `<br>`, `<h2>`-`<h6>`
- **Max length:** 5,000 characters
- **Requires:** `facebook` or `allegro` in project export formats

---

## Batch Processing

- Process 25-50 products per batch
- Use `batch_id` (UUID) in `save_results` for idempotent retries
- Resume after interruption: call `get_status` — pending counts are the checkpoint
- Milestone feedback recommended every 100 products

---

## Reference

- [Merchant API Reference](../api/merchant-reference.md) — full tool signatures and response schemas
- [Product Search](product-search.md) — public product search (no auth)
- [README](../README.md) — project overview
