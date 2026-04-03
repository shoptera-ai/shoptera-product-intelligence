# Cart Actions

Every product returned by the Shoptera API includes a `cart_action` object. This tells you how to add the product to the e-shop's shopping cart.

---

## Cart Action Object

```json
{
  "action": "add_to_cart",
  "url": "https://example.cz/cart/add?id=123",
  "method": "GET",
  "button_text": null
}
```

| Field | Type | Description |
|-------|------|-------------|
| `action` | string | Always `"add_to_cart"` |
| `url` | string | URL to navigate to or interact with |
| `method` | string | One of: `GET`, `browser_click`, `view_product` |
| `button_text` | string or null | Button label to click (only for `browser_click`) |

---

## Method Types

### `GET` — Direct URL Add

Navigate to the URL. The product is added to the cart automatically via a server-side redirect. Common on Shopify, WooCommerce, and many Czech e-shop platforms.

```json
{
  "method": "GET",
  "url": "https://sportshop.cz/cart/add?id=12345",
  "button_text": null
}
```

**How to handle:**
- HTTP client: Send a GET request to `url` (follow redirects).
- Browser: Open `url` in a new tab — product appears in cart.
- AI agent: Tell the user "Product added to cart" and provide the cart URL.

---

### `browser_click` — Button Click Required

Navigate to `url` (the product page), then find and click the button matching `button_text`. This requires browser automation — a simple HTTP request is not enough.

```json
{
  "method": "browser_click",
  "url": "https://fantasyobchod.cz/stribrny-privesek",
  "button_text": "Přidat do košíku"
}
```

**How to handle:**
- Browser automation (Playwright, Puppeteer, Selenium): Navigate to `url`, find the button by its text content, click it.
- Display-only context (chatbot, CLI): Show the product URL and instruct the user to click the add-to-cart button themselves.
- AI agent with MCP browser tool: Use `browser_navigate` to `url`, then `browser_click` on the element matching `button_text`.

**Common button texts:**
- Czech: "Přidat do košíku", "Do košíku", "Koupit"
- Slovak: "Pridať do košíka"
- German: "In den Warenkorb"

---

### `view_product` — Manual Action Required

The e-shop's cart mechanism is unknown. Show the product URL to the user and let them handle it.

```json
{
  "method": "view_product",
  "url": "https://maly-eshop.cz/produkt/456",
  "button_text": null
}
```

**How to handle:**
- Always: Display the product URL as a clickable link.
- AI agent: Say "Visit the product page to add it to your cart" with the link.
- Never attempt automated cart actions with this method.

---

## Decision Matrix

| Capability | `GET` | `browser_click` | `view_product` |
|------------|:-----:|:----------------:|:--------------:|
| HTTP client (requests, fetch) | Auto-add | Show URL | Show URL |
| Browser automation | Auto-add | Click button | Show URL |
| Chat interface | Link to cart | Link to product | Link to product |

---

## Implementation Pattern

```python
def handle_cart_action(product):
    action = product["cart_action"]

    if action["method"] == "GET":
        return {"type": "auto", "url": action["url"]}

    elif action["method"] == "browser_click":
        return {
            "type": "click",
            "url": action["url"],
            "button": action["button_text"],
        }

    elif action["method"] == "view_product":
        return {"type": "manual", "url": action["url"]}
```

See [cart action code examples](../api/examples/cart-actions.md) for complete implementations in Python, JavaScript, and curl.
