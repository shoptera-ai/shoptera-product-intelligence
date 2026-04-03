# Data Coverage

Shoptera indexes product feeds from thousands of e-shops across Central Europe. The catalog is continuously updated as e-shops sync their feeds.

---

## Countries

| Country | Code | Status |
|---------|------|--------|
| Czech Republic | `CZ` | Primary market |
| Slovakia | `SK` | Primary market |
| Poland | `PL` | Growing |
| Hungary | `HU` | Growing |
| Romania | `RO` | Growing |
| Germany | `DE` | Growing |
| Austria | `AT` | Growing |

Use `origin_country` to filter by the e-shop's country. Use `target_country` to filter by markets the e-shop ships to.

---

## Live Statistics

Check current catalog size at any time:

```bash
curl -s "https://api.shoptera.ai/stats/global" | jq '.catalog'
```

Example response:

```json
{
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
```

The stats endpoint is cached for 1 hour and requires no authentication.

---

## Data Freshness

- E-shop feeds are synced on a regular schedule (hourly to daily, depending on the e-shop tier).
- Product availability, prices, and stock status reflect the most recent sync.
- New e-shops are added to the index continuously.

---

## Data Quality

Product data is enriched by AI agents that improve:

- **Titles** — optimized for search discoverability
- **Descriptions** — expanded with attributes from the feed
- **Categories** — standardized across e-shops
- **GTINs** — extracted and validated
- **Condition** — detected from product attributes (new, refurbished, used)
- **Gender/age group** — inferred for apparel products
- **Bundle info** — detected for multipack products

This enrichment means search results are often more accurate than the raw feed data from e-shops.

---

## Coverage Notes

- The `by_country` counts in `/stats/global` refer to the number of e-shops per country, not products.
- A single e-shop may have multiple feeds (e.g., separate feeds for Google Merchant and Heureka).
- Products with GTINs are indexed for barcode lookup; products without GTINs are searchable via semantic and keyword search only.
- Cross-border e-shops appear in their `origin_country` but may list multiple entries in `target_countries`.
