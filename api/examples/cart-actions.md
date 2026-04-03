# Cart Action Examples

Every product returned by the API includes a `cart_action` object. This describes how to add the product to the shopping cart.

There are three action methods. The correct handling depends on your platform's capabilities.

---

## Method 1: `GET`

Navigate to the URL — the product is added to cart automatically. Common on Shopify, WooCommerce, and similar platforms.

```json
{
  "action": "add_to_cart",
  "url": "https://sportshop.cz/cart/add?id=12345",
  "method": "GET",
  "button_text": null
}
```

### Handling

**curl / HTTP client:**

```bash
curl -L "https://sportshop.cz/cart/add?id=12345"
```

**Python:**

```python
import requests

cart_action = product["cart_action"]

if cart_action["method"] == "GET":
    # Navigate to URL — product is added to cart
    response = requests.get(cart_action["url"], allow_redirects=True)
    print(f"Added to cart: {response.url}")
```

**JavaScript:**

```javascript
if (cartAction.method === "GET") {
  // Open URL — product is added to cart
  window.open(cartAction.url, "_blank");
}
```

---

## Method 2: `browser_click`

Navigate to the product page, then click the button matching `button_text`. Requires browser automation (Puppeteer, Playwright, Selenium, etc.).

```json
{
  "action": "add_to_cart",
  "url": "https://fantasyobchod.cz/stribrny-privesek-ametyst",
  "method": "browser_click",
  "button_text": "Přidat do košíku"
}
```

### Handling

**Python (Playwright):**

```python
from playwright.sync_api import sync_playwright

cart_action = product["cart_action"]

if cart_action["method"] == "browser_click":
    with sync_playwright() as p:
        browser = p.chromium.launch(headless=True)
        page = browser.new_page()
        page.goto(cart_action["url"])
        page.click(f"text={cart_action['button_text']}")
        print("Product added to cart via button click")
        browser.close()
```

**JavaScript (Puppeteer):**

```javascript
if (cartAction.method === "browser_click") {
  const browser = await puppeteer.launch();
  const page = await browser.newPage();
  await page.goto(cartAction.url);

  // Click the add-to-cart button by text
  const [button] = await page.$x(
    `//button[contains(text(), '${cartAction.button_text}')]`
  );
  if (button) await button.click();

  await browser.close();
}
```

---

## Method 3: `view_product`

The e-shop platform is unknown. Show the product URL to the user and let them add to cart manually.

```json
{
  "action": "add_to_cart",
  "url": "https://maly-eshop.cz/produkt/123",
  "method": "view_product",
  "button_text": null
}
```

### Handling

**Display-only context (no browser automation):**

```python
cart_action = product["cart_action"]

if cart_action["method"] == "view_product":
    print(f"Visit the product page to add to cart: {cart_action['url']}")
```

**In a chat interface:**

> Here is the product: [Product Name](https://maly-eshop.cz/produkt/123). Visit the link to add it to your cart.

---

## Complete Handler

A unified handler for all three methods:

### Python

```python
def handle_cart_action(product):
    action = product["cart_action"]
    method = action["method"]

    if method == "GET":
        # Auto-add via URL
        import requests
        requests.get(action["url"], allow_redirects=True)
        return f"Added to cart: {product['title']}"

    elif method == "browser_click":
        # Needs browser automation
        return (
            f"Navigate to {action['url']} and click "
            f"'{action['button_text']}' to add to cart"
        )

    elif method == "view_product":
        # Show URL to user
        return f"Visit {action['url']} to add to cart"
```

### JavaScript

```javascript
function handleCartAction(product) {
  const { method, url, button_text } = product.cart_action;

  switch (method) {
    case "GET":
      // Auto-add via URL navigation
      window.open(url, "_blank");
      return `Added to cart: ${product.title}`;

    case "browser_click":
      // Needs automation — navigate + click button
      return `Go to ${url} and click "${button_text}"`;

    case "view_product":
      // Show URL
      return `Visit ${url} to add to cart`;
  }
}
```
