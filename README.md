# Amazon Reviews Scraper API Complete Guide: What Data Can You Actually Get, How to Pull It with ScraperAPI, Which Plan Fits Your Scale, and Why Amazon's May 2026 Update Changed Everything

If you've ever tried to scrape Amazon reviews manually, you know what it feels like to hit a wall at 60 mph. One day the CSS selectors work fine, the next day they're gone. You rotate proxies, Amazon still blocks you. You try again at 3am, get a CAPTCHA. Repeat indefinitely.

The thing is, Amazon reviews aren't just interesting to read — they're data gold. Brands use them to find product flaws before a competitor does. Sellers use them to track rating drifts. Market researchers use them to gauge sentiment at scale. And developers building price-tracking dashboards, competitive intelligence tools, or AI training pipelines all eventually end up staring at the same question: how do I get this review data reliably, without babysitting a scraper 24/7?

This guide covers exactly that — what an **amazon reviews scraper api** actually returns in 2026, how Amazon's platform has changed recently, how to use ScraperAPI's structured endpoint to pull review data, and how to pick the right plan.

---

## What Changed: Amazon's May 2026 Update and What It Means for Review Scrapers

Let's get the bad news out of the way first, because it matters a lot.

In May 2026, Amazon made a significant change to how it serves review content. Review bodies were removed from public product page HTML, and anonymous product-reviews URLs stopped working reliably across the board. In plain English: you can no longer scrape full paginated review lists the way you used to by just hitting the `/product-reviews/` path without being logged in.

What you *can* still get — through structured endpoints that handle the heavy lifting for you — is the **featured reviews section** on a product detail page. This typically includes somewhere between 8 and 13 reviews per product: a curated mix of the most helpful, most recent, and star-filtered feedback. For most use cases — sentiment analysis, quick quality checks, competitive snapshots — this is plenty.

If you need a deeper review corpus for a specific ASIN, strategies include combining the featured reviews data with aggregate rating distributions (which are still available) and running historical trend analysis from data collected before the policy change.

---

## Why Use an Amazon Reviews Scraper API Instead of Rolling Your Own

There's always the DIY route: Python + Requests + BeautifulSoup + a bunch of proxy services duct-taped together. Some developers prefer it. Full control, pay only for proxies.

The problem is the maintenance cost. Amazon runs AWS WAF at the network edge, behavioral fingerprinting to detect non-human request patterns, TLS fingerprinting to identify automated clients, and Cloudflare bot management layered on top. Any of these, when triggered, either silently returns garbage data or blocks you outright. You won't always know which.

A dedicated **amazon reviews scraper api** handles all of this infrastructure automatically:

- Proxy rotation across a large residential IP pool (ScraperAPI runs over 40 million IPs across 50+ countries)
- CAPTCHA solving without you lifting a finger
- JavaScript rendering for dynamically loaded content
- Automatic retries on failed requests
- Geotargeting so you get the right price, availability, and localized content per Amazon marketplace

The tradeoff is cost-per-request vs. your engineering team's time. For most teams, the math favors the API — especially when you factor in what happens the next time Amazon updates its anti-bot stack and breaks your custom scraper again.

---

## How ScraperAPI's Amazon Structured Endpoint Works

ScraperAPI has a dedicated structured data layer specifically for Amazon. Rather than returning raw HTML that you then have to parse yourself, it hands you clean, ready-to-use JSON. Here's the quick breakdown of the Amazon-specific endpoints:

### Amazon Product Page API (Your Main Tool for Reviews)

After the May 2026 changes, this is where your review data lives. The endpoint pulls the full product detail page and returns everything publicly visible — and that includes the featured reviews section.


GET https://api.scraperapi.com/structured/amazon/product?api_key=API_KEY&asin=ASIN&country_code=us&tld=com


The JSON response includes fields like `average_rating`, `total_reviews`, `customer_reviews`, product info, feature bullets, pricing, availability, variant details, and the featured review objects with their star ratings, titles, and full review text. You pass an ASIN, you get structured data back. No parsing.

### Amazon Reviews API (Async Version)

ScraperAPI also has a dedicated async reviews endpoint designed for batch processing:


POST https://async.scraperapi.com/structured/amazon/review


You pass an ASIN (or a list of ASINs), a country code, and optionally a `filterByStar` parameter to narrow results to specific star ratings. The async model means you send the request, get a callback when the data's ready, and don't have to worry about timeouts on large batches.

### Amazon Search API

If you're building a list of products to pull reviews for, start here. Submit a keyword query and get back all ranking products with their ASINs, names, pricing, images, ratings counts, and position data in JSON:


GET https://api.scraperapi.com/structured/amazon/search?api_key=API_KEY&query=your+keyword&country=us


Once you have a list of ASINs from search results (or from best-sellers, or your own product catalog), you feed them into the product or reviews endpoint.

### Amazon Offers API

This one's adjacent but often overlooked in review-scraping workflows. If you're monitoring a competitor's product and want to understand the full seller landscape alongside review sentiment, the offers endpoint returns all available listings for an ASIN:


GET https://api.scraperapi.com/structured/amazon/offers?api_key=API_KEY&asin=ASIN


---

## A Quick Python Example

Here's what pulling Amazon product data (including reviews) looks like with ScraperAPI in Python:

python
import requests
import json

payload = {
    'api_key': 'YOUR_API_KEY',
    'asin': 'B07FTKQ97Q',
    'country_code': 'us',
    'tld': 'com'
}

response = requests.get('https://api.scraperapi.com/structured/amazon/product', params=payload)
data = response.json()

# Access reviews
reviews = data.get('reviews', [])
for review in reviews:
    print(review['title'], review['stars'], review['body'])

# Export everything
with open('amazon_product.json', 'w') as f:
    json.dump(data, f, indent=2)


That's it. No proxy setup, no CAPTCHA handling, no retry logic. ScraperAPI handles all of it on their end.

If you need LLM-ready output instead of JSON (for feeding into a language model or summarization pipeline), just add `output_format=markdown` to your params and the response comes back in clean Markdown format.

---

## Who Actually Uses Amazon Reviews Scraper APIs — and For What

The range of use cases is wider than most people expect:

**Amazon sellers and brands** use review data to monitor product reputation, catch recurring complaints before they compound into rating drops, and track how customers respond to product changes or new versions.

**Market research firms** use aggregate review sentiment to gauge category-level demand signals, identify feature gaps competitors aren't filling, and build consumer insight reports.

**Price intelligence and comparison platforms** combine review scores with pricing data to surface value rankings across categories.

**AI and machine learning teams** use review text as training data for sentiment models, aspect-based opinion mining systems, and product recommendation engines. ScraperAPI even supports LLM-ready output formats specifically for this.

**Affiliate content creators and review sites** use the data to keep product ratings and review counts up to date without manually visiting pages.

**Competitive intelligence tools** track rating drift over time — if a competitor's product drops from 4.5 to 4.1 stars over two months, that's a signal worth investigating.

---

## ScraperAPI Pricing Plans: Which One Makes Sense for Amazon Review Scraping

Amazon product page requests cost 5 credits per request through ScraperAPI (since Amazon is a higher-complexity target). Here's what that looks like across plans:

| Plan | Monthly Price | Annual Price (10% off) | API Credits | Amazon Product Pages (at 5 credits each) | Concurrent Threads | Geotargeting | Link |
|------|--------------|----------------------|-------------|------------------------------------------|-------------------|-------------|------|
| **Hobby** | $49/mo | $44.10/mo | 100,000 | ~20,000 pages | 20 | US & EU only |  [Start Trial](https://www.scraperapi.com/signup?fp_ref=coupons) |
| **Startup** | $149/mo | $134.10/mo | 1,000,000 | ~200,000 pages | 50 | US & EU only |  [Start Trial](https://www.scraperapi.com/signup?fp_ref=coupons) |
| **Business** | $299/mo | $269.10/mo | 3,000,000 | ~600,000 pages | 100 | Global |  [Start Trial](https://www.scraperapi.com/signup?fp_ref=coupons) |
| **Scaling** | $475/mo | $427.50/mo | 5,000,000 | ~1,000,000 pages | 200 | Global |  [Start Trial](https://www.scraperapi.com/signup?fp_ref=coupons) |
| **Professional** | $975/mo | $877.50/mo | 10,500,000 | ~2,100,000 pages | 300 | Global |  [Start Trial](https://www.scraperapi.com/signup?fp_ref=coupons) |
| **Advanced** | $1,975/mo | $1,777.50/mo | 21,500,000 | ~4,300,000 pages | 500 | Global |  [Start Trial](https://www.scraperapi.com/signup?fp_ref=coupons) |
| **Enterprise** | Custom | Custom | 22M+ | Custom | 500+ | Global |  [Contact Sales](https://www.scraperapi.com/contact-sales/?fp_ref=coupons) |

A few things worth noting:

The free trial gives you 5,000 API credits — enough to pull roughly 1,000 Amazon product pages including their review data. No credit card required, so it's genuinely risk-free to test.

Plans from Scaling upward include pay-as-you-go (PAYG) overage, which means if you burn through your monthly credits before renewal, you can keep scraping at a fixed per-credit rate rather than hitting a hard wall.

The Business plan is usually the first tier worth seriously considering for production workflows, because it unlocks global geotargeting. If you're scraping Amazon.de, Amazon.co.jp, or Amazon.in, you need IPs from those regions to get accurate localized data — product prices and availability genuinely differ by geography.

All plans share the same core infrastructure: JS rendering, premium residential IPs, CAPTCHA and anti-bot bypass, rotating proxy pools, custom header support, automatic retries, unlimited bandwidth, and 99.9% uptime.

---

## DataPipeline: If You Don't Want to Write Code

Not every team scraping Amazon reviews is full of developers. ScraperAPI's DataPipeline product is the no-code version of the same infrastructure.

You pick the Amazon template, drop in a list of ASINs or search queries, choose where you want the data delivered (download as JSON/CSV, or push via webhook to your own endpoint), and schedule recurring runs. The pipeline handles everything else.

For marketing teams, product managers, or analysts who need regular Amazon review data without putting a developer request in a queue, this is the practical path.

---

## Things to Keep in Mind

**Amazon costs 5 credits per request**, not 1. Factor this into your plan math before you sign up. The pricing page has a domain cost estimator in the dashboard to check specific URLs.

**Featured reviews only, post-May 2026.** Any tool claiming to return complete paginated review histories for logged-out sessions is either working around an edge case that may break, or not being fully transparent about what they're returning.

**Geotargeting matters.** Amazon serves different prices, availability, and sometimes different review sets depending on the visitor's location. If you care about a specific marketplace, use the `country_code` and `tld` parameters to get accurate data for that region.

**Async for bulk jobs.** If you're processing hundreds or thousands of ASINs, use the async endpoint. Synchronous requests time out on large batches. The async + webhook pattern scales much more cleanly.

---

## Getting Started

The shortest path from zero to Amazon review data:

1. 👉 [Sign up for a free ScraperAPI trial](https://www.scraperapi.com/signup?fp_ref=coupons) — 5,000 credits, no card needed
2. Grab your API key from the dashboard
3. Call `https://api.scraperapi.com/structured/amazon/product?api_key=YOUR_KEY&asin=YOUR_ASIN&country_code=us`
4. Parse the JSON response for the `reviews` field

If you're evaluating at larger scale or want to test with custom concurrency limits before committing to a paid plan, 👉 [contact the ScraperAPI sales team](https://www.scraperapi.com/contact-sales/?fp_ref=coupons) for a custom trial with higher credit allocation and dedicated onboarding.

Amazon review data isn't as freely flowing as it used to be — but with the right API infrastructure in place, it's still completely accessible at scale.
