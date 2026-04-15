# Generic HTTP Examples

Standalone examples for all Shoptera API endpoints. No SDK or authentication required.

Base URL: `https://shoptera.ai/api`

---

## Semantic Search

### curl

```bash
# Search for gardening gifts under 500 CZK
curl -s "https://shoptera.ai/api/v1/search?q=d%C3%A1rek+pro+zahradn%C3%ADka&max_price=500&currency=CZK&origin_country=CZ&limit=5"

# Search with brand and availability filter
curl -s "https://shoptera.ai/api/v1/search?q=b%C4%9B%C5%BEeck%C3%A9+boty&brand=Nike&availability=in_stock&limit=10"

# Search across all countries
curl -s "https://shoptera.ai/api/v1/search?q=wireless+headphones&max_price=100&currency=EUR"

# Only return specific fields (up to 70% smaller response)
curl -s "https://shoptera.ai/api/v1/search?q=boty&limit=5&fields=title,price,product_url,cart_action"
```

### Python

```python
import requests

def semantic_search(query, **filters):
    params = {"q": query, **filters}
    response = requests.get("https://shoptera.ai/api/v1/search", params=params)
    response.raise_for_status()
    return response.json()

# Example
results = semantic_search(
    "dárek pro zahradníka",
    max_price=500,
    currency="CZK",
    origin_country="CZ",
    limit=5,
)

for product in results["results"]:
    print(f"{product['title']} — {product['price']} {product['currency']} (score: {product['score']:.2f})")
```

### JavaScript

```javascript
async function semanticSearch(query, filters = {}) {
  const params = new URLSearchParams({ q: query, ...filters });
  const response = await fetch(
    `https://shoptera.ai/api/v1/search?${params}`
  );
  if (!response.ok) throw new Error(`HTTP ${response.status}`);
  return response.json();
}

// Example
const results = await semanticSearch("dárek pro zahradníka", {
  max_price: "500",
  currency: "CZK",
  origin_country: "CZ",
  limit: "5",
});

for (const product of results.results) {
  console.log(
    `${product.title} — ${product.price} ${product.currency} (score: ${product.score.toFixed(2)})`
  );
}
```

---

## Keyword Search

### curl

```bash
# Search by product name
curl -s "https://shoptera.ai/api/v1/search/text?title=st%C5%99%C3%ADbrn%C3%BD+p%C5%99%C3%ADv%C4%9Bsek&origin_country=CZ"

# Search with brand filter
curl -s "https://shoptera.ai/api/v1/search/text?title=Air+Max&brand=Nike&limit=10"

# Only return specific fields
curl -s "https://shoptera.ai/api/v1/search/text?title=Air+Max&brand=Nike&fields=title,price,product_url"
```

### Python

```python
import requests

def keyword_search(title, **filters):
    params = {"title": title, **filters}
    response = requests.get("https://shoptera.ai/api/v1/search/text", params=params)
    response.raise_for_status()
    return response.json()

# Example
results = keyword_search("stříbrný přívěsek", origin_country="CZ", limit=5)

for product in results["results"]:
    print(f"{product['title']} — {product['price']} {product['currency']}")
    print(f"  {product['eshop_name']} ({product['eshop_domain']})")
```

### JavaScript

```javascript
async function keywordSearch(title, filters = {}) {
  const params = new URLSearchParams({ title, ...filters });
  const response = await fetch(
    `https://shoptera.ai/api/v1/search/text?${params}`
  );
  if (!response.ok) throw new Error(`HTTP ${response.status}`);
  return response.json();
}

// Example
const results = await keywordSearch("stříbrný přívěsek", {
  origin_country: "CZ",
  limit: "5",
});

for (const product of results.results) {
  console.log(`${product.title} — ${product.price} ${product.currency}`);
  console.log(`  ${product.eshop_name} (${product.eshop_domain})`);
}
```

---

## GTIN/EAN Lookup

### curl

```bash
# Look up by EAN
curl -s "https://shoptera.ai/api/v1/search/gtin/5901234123457?origin_country=CZ"

# Look up across all countries
curl -s "https://shoptera.ai/api/v1/search/gtin/5901234123457?limit=20"

# Only return price comparison fields
curl -s "https://shoptera.ai/api/v1/search/gtin/5901234123457?fields=title,price,currency,eshop_name,product_url"
```

### Python

```python
import requests

def gtin_lookup(gtin, **filters):
    response = requests.get(f"https://shoptera.ai/api/v1/search/gtin/{gtin}", params=filters)
    response.raise_for_status()
    return response.json()

# Example — price comparison
results = gtin_lookup("5901234123457", origin_country="CZ")

print(f"Found at {results['total_found']} e-shops:")
for product in sorted(results["results"], key=lambda p: p["price"]):
    print(f"  {product['eshop_name']}: {product['price']} {product['currency']}")
```

### JavaScript

```javascript
async function gtinLookup(gtin, filters = {}) {
  const params = new URLSearchParams(filters);
  const response = await fetch(
    `https://shoptera.ai/api/v1/search/gtin/${gtin}?${params}`
  );
  if (!response.ok) throw new Error(`HTTP ${response.status}`);
  return response.json();
}

// Example — price comparison
const results = await gtinLookup("5901234123457", { origin_country: "CZ" });

console.log(`Found at ${results.total_found} e-shops:`);
results.results
  .sort((a, b) => a.price - b.price)
  .forEach((p) => console.log(`  ${p.eshop_name}: ${p.price} ${p.currency}`));
```

---

## Global Stats

### curl

```bash
curl -s "https://shoptera.ai/api/stats/global" | jq '.'
```

### Python

```python
import requests

response = requests.get("https://shoptera.ai/api/stats/global")
stats = response.json()

catalog = stats["catalog"]
print(f"E-shops: {catalog['eshops_count']}")
print(f"Products: {catalog['products_count']:,}")
print(f"Countries:")
for country in catalog["by_country"]:
    print(f"  {country['label']}: {country['count']} e-shops")
```

### JavaScript

```javascript
const response = await fetch("https://shoptera.ai/api/stats/global");
const stats = await response.json();

const { catalog } = stats;
console.log(`E-shops: ${catalog.eshops_count}`);
console.log(`Products: ${catalog.products_count.toLocaleString()}`);
console.log("Countries:");
for (const country of catalog.by_country) {
  console.log(`  ${country.label}: ${country.count} e-shops`);
}
```

---

## Error Handling

### Python

```python
import requests

def search_with_error_handling(query, **filters):
    params = {"q": query, **filters}

    try:
        response = requests.get("https://shoptera.ai/api/v1/search", params=params)

        if response.status_code == 422:
            errors = response.json()["detail"]
            print(f"Validation error: {errors}")
            return None

        if response.status_code == 429:
            retry_after = response.headers.get("Retry-After", "60")
            print(f"Rate limited. Retry after {retry_after} seconds.")
            return None

        response.raise_for_status()
        return response.json()

    except requests.RequestException as e:
        print(f"Request failed: {e}")
        return None
```

### JavaScript

```javascript
async function searchWithErrorHandling(query, filters = {}) {
  const params = new URLSearchParams({ q: query, ...filters });

  try {
    const response = await fetch(
      `https://shoptera.ai/api/v1/search?${params}`
    );

    if (response.status === 422) {
      const { detail } = await response.json();
      console.error("Validation error:", detail);
      return null;
    }

    if (response.status === 429) {
      const retryAfter = response.headers.get("Retry-After") || "60";
      console.error(`Rate limited. Retry after ${retryAfter}s.`);
      return null;
    }

    if (!response.ok) {
      throw new Error(`HTTP ${response.status}`);
    }

    return await response.json();
  } catch (error) {
    console.error("Request failed:", error.message);
    return null;
  }
}
```

---

## Rate Limits

- **300 requests per hour** per IP address
- Shared across all endpoints (REST + MCP)
- 429 responses include a `Retry-After` header
- Cache results to minimize requests for repeated queries

---

## Merchant Feed Optimizer

For e-shop owners: authenticated API for AI-driven product feed optimization.

**Base URL:** `https://shoptera.ai/api/merchant`

**MCP endpoint:** `https://shoptera.ai/mcp/merchant/`

**Authentication:** `Authorization: Bearer shopt_YOUR_KEY`

### curl — List Projects

```bash
curl -s -H "Authorization: Bearer shopt_YOUR_KEY" \
  "https://shoptera.ai/api/merchant/projects"
```

### curl — Get Feed Status

```bash
curl -s -H "Authorization: Bearer shopt_YOUR_KEY" \
  "https://shoptera.ai/api/merchant/projects/my-eshop/status"
```

### curl — Get Priority Products

```bash
curl -s -H "Authorization: Bearer shopt_YOUR_KEY" \
  "https://shoptera.ai/api/merchant/projects/my-eshop/priority?limit=10"
```

### Python — Full Workflow

```python
import requests

API_KEY = "shopt_YOUR_KEY"
BASE = "https://shoptera.ai/api/merchant"
HEADERS = {"Authorization": f"Bearer {API_KEY}"}

# 1. List projects
projects = requests.get(f"{BASE}/projects", headers=HEADERS).json()
slug = projects["projects"][0]["slug"]

# 2. Check status
status = requests.get(f"{BASE}/projects/{slug}/status", headers=HEADERS).json()
print(f"Pending: {status['pending_optimization']} products")
print(f"Recommended: {status['recommended_action']['workflow']}")

# 3. Get products missing GTIN
products = requests.get(
    f"{BASE}/projects/{slug}/products",
    headers=HEADERS,
    params={"filter": '{"missing": ["gtin"]}', "limit": 25},
).json()

print(f"Products without GTIN: {products['count']}")
```

See [Merchant Workflows](../../capabilities/merchant-workflows.md) and [Merchant API Reference](../../api/merchant-reference.md) for full documentation.
