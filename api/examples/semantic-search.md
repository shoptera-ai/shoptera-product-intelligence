# Semantic Search Examples

Search products using natural language. The API understands intent, synonyms, and context through vector embeddings.

**Scenario:** Find a gift for a gardener under 500 CZK.

---

## curl

```bash
curl -s "https://shoptera.ai/api/v1/search?q=d%C3%A1rek+pro+zahradn%C3%ADka+do+500+K%C4%8D&max_price=500&currency=CZK&origin_country=CZ&limit=5"
```

With filters:

```bash
curl -s "https://shoptera.ai/api/v1/search?q=b%C4%9B%C5%BEeck%C3%A9+boty&brand=Nike&availability=in_stock&min_price=1000&max_price=3000&currency=CZK"
```

---

## Python

```python
import requests

response = requests.get("https://shoptera.ai/api/v1/search", params={
    "q": "dárek pro zahradníka do 500 Kč",
    "max_price": 500,
    "currency": "CZK",
    "origin_country": "CZ",
    "limit": 5,
})

data = response.json()

for product in data["results"]:
    print(f"{product['title']} — {product['price']} {product['currency']}")
    print(f"  Score: {product['score']:.2f}")
    print(f"  URL: {product['product_url']}")
    print()
```

---

## JavaScript

```javascript
const params = new URLSearchParams({
  q: "dárek pro zahradníka do 500 Kč",
  max_price: "500",
  currency: "CZK",
  origin_country: "CZ",
  limit: "5",
});

const response = await fetch(
  `https://shoptera.ai/api/v1/search?${params}`
);
const data = await response.json();

for (const product of data.results) {
  console.log(`${product.title} — ${product.price} ${product.currency}`);
  console.log(`  Score: ${product.score.toFixed(2)}`);
  console.log(`  URL: ${product.product_url}`);
}
```

---

## Tips

- Semantic search understands Czech, Slovak, German, and English queries.
- Queries like "gift for a gardener" work just as well as "dárek pro zahradníka".
- Use `max_price` and `currency` together for price-bounded searches.
- The `score` field (0-1) indicates semantic relevance — higher is better.
- Combine with `origin_country` to search e-shops from a specific country.
