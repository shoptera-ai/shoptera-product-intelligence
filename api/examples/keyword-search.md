# Keyword Search Examples

Search products by exact keyword matching in titles. Deterministic, fast, and best when you know the product name.

**Scenario:** Find a silver pendant.

---

## curl

```bash
curl -s "https://api.shoptera.ai/api/v1/search/text?title=st%C5%99%C3%ADbrn%C3%BD+p%C5%99%C3%ADv%C4%9Bsek&origin_country=CZ&limit=5"
```

With brand filter:

```bash
curl -s "https://api.shoptera.ai/api/v1/search/text?title=p%C5%99%C3%ADv%C4%9Bsek&brand=Fantasy+%C5%A0perky&origin_country=CZ"
```

---

## Python

```python
import requests

response = requests.get("https://api.shoptera.ai/api/v1/search/text", params={
    "title": "stříbrný přívěsek",
    "origin_country": "CZ",
    "limit": 5,
})

data = response.json()

for product in data["results"]:
    print(f"{product['title']} — {product['price']} {product['currency']}")
    print(f"  Brand: {product['brand']}")
    print(f"  E-shop: {product['eshop_name']} ({product['eshop_domain']})")
    print()
```

---

## JavaScript

```javascript
const params = new URLSearchParams({
  title: "stříbrný přívěsek",
  origin_country: "CZ",
  limit: "5",
});

const response = await fetch(
  `https://api.shoptera.ai/api/v1/search/text?${params}`
);
const data = await response.json();

for (const product of data.results) {
  console.log(`${product.title} — ${product.price} ${product.currency}`);
  console.log(`  Brand: ${product.brand}`);
  console.log(`  E-shop: ${product.eshop_name} (${product.eshop_domain})`);
}
```

---

## Tips

- Keyword search matches tokens in product titles — order does not matter.
- No `score` field is returned (results are not ranked by relevance).
- Use keyword search when you know the exact product name or model number.
- Combine with `brand` for precise results: `?title=air+max&brand=Nike`.
- Faster than semantic search — prefer it for known product lookups.
