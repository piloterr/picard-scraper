<p align="center">
  <a href="https://www.piloterr.com"><img src="images/banner.png" alt="Picard Scraper API by Piloterr – scrape Picard data as JSON" width="100%"></a>
</p>

# Picard Scraper API

[![Discord](https://img.shields.io/badge/Discord-Join%20the%20community-5865F2?logo=discord&logoColor=white)](https://discord.gg/CFj3ph8aHU) [![Get API key](https://img.shields.io/badge/Piloterr-Get%20a%20free%20API%20key-111827)](https://app.piloterr.com) [![API docs](https://img.shields.io/badge/Docs-OpenAPI-0EA5E9)](https://app.piloterr.com/openapi) [![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

The **Piloterr Picard Scraper API** lets you extract public data from Picard as **clean, structured JSON** — no proxies, headless browsers or HTML parsing to maintain. Endpoints available in production:

- **Picard Product API** — Picard Product API.
- **Picard Search API** — Picard Search API.

Use it for price monitoring, assortment tracking, competitor analysis, product research or catalog enrichment.

| Endpoint | Path | Credits | What you get |
|---|---|---|---|
| [Picard Product API](#picard-product-api) | `/v2/picard/product` | 1 | Picard Product API. Fetch full frozen food product data from a Picard URL or reference. Returns pricing, images, description, Nutri-Score, Planet-Score, breadcrumb and reviews. |
| [Picard Search API](#picard-search-api) | `/v2/picard/search` | 1 | Picard Search API. Search frozen food listings on picard.fr by keyword or URL. Parses SFCC Search-UpdateGrid HTML; returns prices, Nutri-Score, ratings and pagination. |

## Table of contents

- [How it works](#how-it-works)
- [Picard Product API](#picard-product-api)
- [Picard Search API](#picard-search-api)
- [Code examples in other languages](#code-examples-in-other-languages)
- [Practical use cases](#practical-use-cases)
- [Why Piloterr?](#why-piloterr)
- [FAQ](#faq)
- [Related scrapers](#related-scrapers)
- [Support](#support)

## How it works

1. **Create a free Piloterr account** and copy your API key from the [dashboard](https://app.piloterr.com).
2. **Send a `GET` (or `POST`) request** to `https://api.piloterr.com/v2/...` with the header `x-api-key: YOUR_API_KEY`.
3. **Receive structured JSON** — Piloterr handles rendering, proxies, retries and parsing.

```bash
export PILOTERR_API_KEY="your-api-key"
pip install requests        # Python
# TypeScript: Node.js 18+ (native fetch), run with `npx tsx file.ts`
```

> Every endpoint accepts both `GET` (query string) and `POST` (JSON body with the same fields).

## Picard Product API

Picard Product API. Fetch full frozen food product data from a Picard URL or reference. Returns pricing, images, description, Nutri-Score, Planet-Score, breadcrumb and reviews.

`GET | POST https://api.piloterr.com/v2/picard/product` · **1 credit per request** · [Full reference](https://app.piloterr.com/openapi)

### Request parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `query` | string | ✅ | Picard product URL or numeric reference. |
| `return_page_source` | boolean | — | When `true`, return the raw HTML as `{"result": "..."}` instead of structured JSON. Defaults to `false`. |

### Python example

```python
import json
import os
import requests

response = requests.get(
    "https://api.piloterr.com/v2/picard/product",
    headers={"x-api-key": os.environ["PILOTERR_API_KEY"]},
    params={
        "query": "050314",
    },
    timeout=60,
)
response.raise_for_status()
data = response.json()

print(json.dumps(data, indent=2, ensure_ascii=False)[:2000])
```

### TypeScript example

```typescript
type PicardProduct = {
  url?: string;
  gtin?: string;
  brand?: string;
  label?: string;
  price?: number;
  range?: string;
  title?: string;
  images?: string[];
  origin?: string;
  rating?: number;
  category?: string;
  currency?: string;
  image_url?: string;
  packaging?: string;
  reference?: string;
  breadcrumb?: {
    url?: string;
    name?: string;
    position?: number;
  }[];
  nutriscore?: string;
  product_id?: string;
  unit_price?: number;
  description?: string;
  planetscore?: string;
  subcategory?: string;
  availability?: string;
  subcategory2?: string;
  reviews_count?: number;
  [key: string]: unknown;
};

const params = new URLSearchParams({
  "query": "050314",
});

const response = await fetch(`https://api.piloterr.com/v2/picard/product?${params}`, {
  headers: { "x-api-key": process.env.PILOTERR_API_KEY! },
});
if (!response.ok) throw new Error(`Piloterr error ${response.status}: ${await response.text()}`);

const data = (await response.json()) as PicardProduct;

console.log(JSON.stringify(data, null, 2).slice(0, 2000));
```

<details>
<summary><b>POST variant</b></summary>

```http
POST https://api.piloterr.com/v2/picard/product
x-api-key: YOUR_API_KEY
Content-Type: application/json

{"query": "050314"}
```
</details>

### Output example

Example response from the API reference, abridged — real values depend on the page you scrape.

```json
{
  "url": "https://www.picard.fr/produits/puree-patate-douce-000000000000050314.html",
  "gtin": "3270160503148",
  "brand": "Picard",
  "label": "Convient aux végétariens",
  "price": 2.6,
  "range": "Purées",
  "title": "Purée de patate douce",
  "images": [
    "https://www.picard.fr/dw/image/v2/AAHV_PRD/on/demandware.static/-/Sites-catalog-picard/default/dwea0e1bd9/produits/legumes/edition/000000…",
    "https://www.picard.fr/dw/image/v2/AAHV_PRD/on/demandware.static/-/Sites-catalog-picard/default/dwea0e1bd9/produits/legumes/edition/000000…"
  ],
  "origin": "Produit élaboré en France à partir de patate douce cultivée en Espagne et au Portugal",
  "rating": 4.7,
  "category": "Légumes et fruits",
  "currency": "EUR",
  "image_url": "https://www.picard.fr/dw/image/v2/AAHV_PRD/on/demandware.static/-/Sites-catalog-picard/default/dwea0e1bd9/produits/legumes/edition/000000…",
  "packaging": "la boîte de 450 g",
  "reference": "050314",
  "breadcrumb": [
    {
      "url": "https://www.picard.fr/",
      "name": "Accueil",
      "position": 1
    },
    {
      "url": "https://www.picard.fr/rayons",
      "name": "Rayons",
      "position": 2
    }
  ],
  "nutriscore": "A",
  "product_id": "000000000000050314",
  "unit_price": 5.77,
  "description": "Redécouvrez un légume oublié avec cette purée de patate douce au petit goût de châtaigne.",
  "planetscore": "C",
  "subcategory": "Légumes cuisinés",
  "availability": "InStock",
  "subcategory2": "Purées",
  "reviews_count": 336
}
```

### Response fields

| Field | Type | Description |
|---|---|---|
| `url` | string |  |
| `gtin` | string |  |
| `brand` | string |  |
| `label` | string |  |
| `price` | number |  |
| `range` | string |  |
| `title` | string |  |
| `images` | string[] |  |
| `origin` | string |  |
| `rating` | number |  |
| `category` | string |  |
| `currency` | string |  |
| `image_url` | string |  |
| `packaging` | string |  |
| `reference` | string |  |
| `breadcrumb[]` | object[] |  |
| `breadcrumb[].url` | string |  |
| `breadcrumb[].name` | string |  |
| `breadcrumb[].position` | integer |  |
| `nutriscore` | string |  |
| `product_id` | string |  |
| `unit_price` | number |  |
| `description` | string |  |
| `planetscore` | string |  |
| `subcategory` | string |  |

## Picard Search API

Picard Search API. Search frozen food listings on picard.fr by keyword or URL. Parses SFCC Search-UpdateGrid HTML; returns prices, Nutri-Score, ratings and pagination.

`GET | POST https://api.piloterr.com/v2/picard/search` · **1 credit per request** · [Full reference](https://app.piloterr.com/openapi)

### Request parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `query` | string | ✅ | Search keyword or full Picard search URL. |
| `page` | number | — | 1-based page number (24 results per page via SFCC `Search-UpdateGrid`). Default: `1`. |
| `return_page_source` | boolean | — | When `true`, return the raw HTML as `{"result": "..."}` instead of structured JSON. Defaults to `false`. |

### Python example

```python
import json
import os
import requests

response = requests.get(
    "https://api.piloterr.com/v2/picard/search",
    headers={"x-api-key": os.environ["PILOTERR_API_KEY"]},
    params={
        "query": "patate douce",
    },
    timeout=60,
)
response.raise_for_status()
data = response.json()

print(json.dumps(data, indent=2, ensure_ascii=False)[:2000])
```

### TypeScript example

```typescript
type PicardSearch = {
  results?: {
    url?: string;
    price?: number;
    title?: string;
    rating?: number;
    category?: string;
    currency?: string;
    image_url?: string;
    packaging?: string;
    reference?: string;
    nutriscore?: string;
    product_id?: string;
    subcategory?: string;
    availability?: string;
    reviews_count?: unknown;
    unit_price_text?: string;
  }[];
  pagination?: {
    next?: unknown;
    page?: number;
    count?: number;
    has_next?: boolean;
    per_page?: number;
  };
};

const params = new URLSearchParams({
  "query": "patate douce",
});

const response = await fetch(`https://api.piloterr.com/v2/picard/search?${params}`, {
  headers: { "x-api-key": process.env.PILOTERR_API_KEY! },
});
if (!response.ok) throw new Error(`Piloterr error ${response.status}: ${await response.text()}`);

const data = (await response.json()) as PicardSearch;

console.log(JSON.stringify(data, null, 2).slice(0, 2000));
```

<details>
<summary><b>POST variant</b></summary>

```http
POST https://api.piloterr.com/v2/picard/search
x-api-key: YOUR_API_KEY
Content-Type: application/json

{"query": "patate douce"}
```
</details>

### Output example

Example response from the API reference, abridged — real values depend on the page you scrape.

```json
{
  "results": [
    {
      "url": "https://www.picard.fr/produits/inedites-patate-douce-patates-douce-enrobees-000000000000050935.html",
      "price": 3.4,
      "title": "Inédites à la patate douce, patates douce enrobées",
      "rating": 4.56,
      "category": "Légumes et fruits",
      "currency": "EUR",
      "image_url": "https://www.picard.fr/dw/image/v2/AAHV_PRD/on/demandware.static/-/Sites-catalog-picard/default/dw8a619f76/produits/000000000000050935_E.j…",
      "packaging": "le sachet de 500 g",
      "reference": "50935",
      "nutriscore": "C",
      "product_id": "000000000000050935",
      "subcategory": "Féculents",
      "availability": "en stock",
      "reviews_count": null,
      "unit_price_text": "6,80 €/kg"
    },
    {
      "url": "https://www.picard.fr/produits/puree-patate-douce-000000000000050314.html",
      "price": 2.6,
      "title": "Purée de patate douce",
      "rating": 4.7,
      "category": "Légumes et fruits",
      "currency": "EUR",
      "image_url": "https://www.picard.fr/dw/image/v2/AAHV_PRD/on/demandware.static/-/Sites-catalog-picard/default/dwea0e1bd9/produits/legumes/edition/000000…",
      "packaging": "la boîte de 450 g",
      "reference": "50314",
      "nutriscore": "A",
      "product_id": "000000000000050314",
      "subcategory": "Légumes cuisinés",
      "availability": "en stock",
      "reviews_count": null,
      "unit_price_text": "5,77 €/kg"
    }
  ],
  "pagination": {
    "next": null,
    "page": 1,
    "count": 21,
    "has_next": false,
    "per_page": 24
  }
}
```

### Response fields

| Field | Type | Description |
|---|---|---|
| `results[]` | object[] |  |
| `results[].url` | string |  |
| `results[].price` | number |  |
| `results[].title` | string |  |
| `results[].rating` | number |  |
| `results[].category` | string |  |
| `results[].currency` | string |  |
| `results[].image_url` | string |  |
| `results[].packaging` | string |  |
| `results[].reference` | string |  |
| `results[].nutriscore` | string |  |
| `results[].product_id` | string |  |
| `results[].subcategory` | string |  |
| `results[].availability` | string |  |
| `results[].reviews_count` | object |  |
| `results[].unit_price_text` | string |  |
| `pagination` | object |  |
| `pagination.next` | object |  |
| `pagination.page` | integer |  |
| `pagination.count` | integer |  |
| `pagination.has_next` | boolean |  |
| `pagination.per_page` | integer |  |

## Code examples in other languages

Ready-to-run examples live in the [`examples/`](examples) folder. Each one reads your key from the `PILOTERR_API_KEY` environment variable.

| Language | Product | Search |
|---|---|---|
| cURL (Bash) | [`product`](examples/curl/product.sh) | [`search`](examples/curl/search.sh) |
| PHP | [`product`](examples/php/product.php) | [`search`](examples/php/search.php) |
| C# | [`product`](examples/csharp/Product.cs) | [`search`](examples/csharp/Search.cs) |
| Go | [`product`](examples/go/product.go) | [`search`](examples/go/search.go) |
| Java | [`product`](examples/java/ProductExample.java) | [`search`](examples/java/SearchExample.java) |
| Ruby | [`product`](examples/ruby/product.rb) | [`search`](examples/ruby/search.rb) |
| Node.js | [`product`](examples/nodejs/product.mjs) | [`search`](examples/nodejs/search.mjs) |

## Practical use cases

- **Price monitoring** — Track prices, promotions and availability over time to react to competitors.
- **Assortment & catalog tracking** — Follow new products, delisted items and category changes.
- **Competitor analysis** — Benchmark ranges, brands and positioning across retailers.
- **Product research** — Spot best-sellers and trends from search results.
- **Catalog enrichment** — Pull titles, images and attributes into your PIM or marketplace listings.

## Why Piloterr?

- **No infrastructure** — no proxies, headless browsers or CAPTCHA solving to run yourself.
- **Structured JSON** — stable, documented fields instead of brittle HTML selectors.
- **Pay per request** — transparent credit cost per endpoint, failed requests are retried for you.
- **One API key, hundreds of sources** — Picard plus many other e-commerce, search and company data endpoints in the [Piloterr library](https://www.piloterr.com/library).
- **Any language** — plain HTTPS + JSON, with examples for Python, TypeScript, cURL, PHP, C#, Go, Java, Ruby and Node.js.

## FAQ

### Is it legal to scrape Picard?

Collecting publicly available data is generally allowed in many jurisdictions, but rules depend on your country, the data you collect and how you use it. Avoid personal data unless you have a lawful basis, respect the website's terms where they apply, and get legal advice for your specific use case. This is not legal advice.

### Do I need proxies or a headless browser?

No. Piloterr handles rendering, proxy rotation and retries on its side. You only send an HTTPS request with your `x-api-key` header and receive JSON.

### How much does a request cost?

Credits per call: `/v2/picard/product` **1**, `/v2/picard/search` **1**. See the [Piloterr website](https://www.piloterr.com) for plans.

### What data does the Picard API return?

Each endpoint returns structured JSON: **Picard Product** — `url`, `gtin`, `brand`, `label`, `price`, `range`; **Picard Search** — `results[]`, `results[].url`, `results[].price`, `results[].title`, `results[].rating`, `results[].category`.

### How do I paginate Picard results?

Use the `page` parameter and keep requesting while the response indicates more results.

### Can I get the raw HTML instead of JSON?

Yes. Set `return_page_source=true` and the response becomes `{"result": "..."}` with the page source.

### Which languages are supported?

Any language that can make an HTTP request. This README shows Python and TypeScript; the [`examples/`](examples) folder contains cURL, PHP, C#, Go, Java, Ruby and Node.js.

## Related scrapers

- [About You Scraper API](https://github.com/piloterr/aboutyou-scraper)
- [Action Scraper API](https://github.com/piloterr/action-scraper)
- [Aldi Scraper API](https://github.com/piloterr/aldi-scraper)
- [Alibaba Scraper API](https://github.com/piloterr/alibaba-scraper)
- [AliExpress Scraper API](https://github.com/piloterr/aliexpress-scraper)
- [Allegro Scraper API](https://github.com/piloterr/allegro-scraper)
- [All Piloterr scrapers on GitHub](https://github.com/piloterr)

## Support

- 💬 Join the [Piloterr Discord](https://discord.gg/CFj3ph8aHU) to ask questions and share what you build.
- 📚 Browse the [API reference (OpenAPI)](https://app.piloterr.com/openapi) and the [Piloterr library](https://www.piloterr.com/library).
- 🔑 [Get your API key](https://app.piloterr.com).

---

<p align="center">
  <a href="https://www.piloterr.com">
    <picture>
      <source media="(prefers-color-scheme: dark)" srcset="images/logo-dark.svg">
      <img src="images/logo.svg" alt="Piloterr" height="32">
    </picture>
  </a>
  <br>
  <sub>Built by <a href="https://www.piloterr.com">Piloterr</a> — web scraping APIs for e-commerce, search, real estate and more, one API key.</sub>
</p>

<sub>Picard is a trademark of its respective owner. This repository is not affiliated with or endorsed by Picard. Examples are released under the MIT license.</sub>
