# GTIN/EAN Lookup Examples

Look up products by barcode number. Find which e-shops sell a specific product and compare prices.

**Scenario:** Find e-shops selling the product with EAN 5901234123457.

---

## curl

```bash
curl -s "https://shoptera.ai/api/v1/search/gtin/5901234123457?origin_country=CZ&limit=10"
```

Without country filter (search all countries):

```bash
curl -s "https://shoptera.ai/api/v1/search/gtin/5901234123457"
```

---

## Python

```python
import requests

gtin = "5901234123457"

response = requests.get(f"https://shoptera.ai/api/v1/search/gtin/{gtin}", params={
    "origin_country": "CZ",
    "limit": 10,
})

data = response.json()

print(f"Found {data['total_found']} e-shops selling GTIN {gtin}:\n")

for product in data["results"]:
    print(f"  {product['eshop_name']} ({product['eshop_domain']})")
    print(f"    Price: {product['price']} {product['currency']}")
    print(f"    Availability: {product['availability']}")
    print(f"    URL: {product['product_url']}")
    print()
```

---

## JavaScript

```javascript
const gtin = "5901234123457";

const params = new URLSearchParams({
  origin_country: "CZ",
  limit: "10",
});

const response = await fetch(
  `https://shoptera.ai/api/v1/search/gtin/${gtin}?${params}`
);
const data = await response.json();

console.log(`Found ${data.total_found} e-shops selling GTIN ${gtin}:\n`);

for (const product of data.results) {
  console.log(`  ${product.eshop_name} (${product.eshop_domain})`);
  console.log(`    Price: ${product.price} ${product.currency}`);
  console.log(`    Availability: ${product.availability}`);
  console.log(`    URL: ${product.product_url}`);
}
```

---

## Tips

- GTIN must be 8-14 digits (covers EAN-8, EAN-13, UPC-A, and GTIN-14).
- Use this for price comparison across e-shops selling the same product.
- No `score` field is returned — results are exact barcode matches.
- Combine with `origin_country` to limit results to a specific market.
- Not all products have GTINs — use semantic or keyword search as fallback.
