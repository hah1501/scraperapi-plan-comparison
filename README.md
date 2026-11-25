# Scraping API Guide: Is ScraperAPI Worth It, Where Does the 50MB Limit Bite, and Which Plan Do You Actually Need? (All Six Access Methods Compared)

ScraperAPI is a managed fetch layer for public web pages: you send a URL with your API key, and it comes back as HTML after proxy rotation, browser rendering and CAPTCHA handling are dealt with for you. Pricing starts at a free tier with 1,000 credits a month and 5 concurrent connections (5,000 credits for your first seven days), then Hobby at $49/month and Startup at $149/month for 1,000,000 credits and 50 threads.

Credit multipliers decide your bill, not the sticker price. A plain article costs 1 credit, an Amazon product page costs 5, a Google results page costs 25. At Startup's $149 for 1,000,000 credits, that budget is either a million ordinary pages or 40,000 Google SERPs, and the gap between those two numbers is where most plan decisions go wrong.

Below: all eight plans, the six ways to send a request (three of which cover almost everyone), the two hard limits worth testing against your own target list before you pay, and where Firecrawl or Scrapy.io fit better.

> **Quick take:** Start on the free tier, move to Hobby at $49/month, and only climb when the Domain Cost Estimator shows a real multiplier on your target domains. If your traffic is mostly Google or Amazon, size on multiplier-adjusted requests rather than raw credits. 👉 [Start a free ScraperAPI account and check your own URLs](https://www.scraperapi.com/?fp_ref=coupons)

## Every ScraperAPI plan, side by side

ScraperAPI sells three variables: monthly credits, concurrent threads, and geotargeting. Geotargeting is the one that quietly forces an upgrade, because country-level targeting outside the US and EU starts at Business.

| Plan | Credits / month | Concurrent threads | Geotargeting | Price | Get started |
| --- | --- | --- | --- | --- | --- |
| Free | 1,000 | 5 | Not listed | $0 | [Open a free account](https://www.scraperapi.com/?fp_ref=coupons) |
| Hobby | 100,000 | 20 | US & EU | $49/month | [Start on Hobby](https://www.scraperapi.com/?fp_ref=coupons) |
| Startup | 1,000,000 | 50 | US & EU | $149/month, or $134.10/month billed annually | [Start on Startup](https://www.scraperapi.com/?fp_ref=coupons) |
| Business | 3,000,000 | 100 | Country-level | $299/month | [Start on Business](https://www.scraperapi.com/?fp_ref=coupons) |
| Scaling | 5,000,000 | 200 | Country-level | Quoted per tier | [Enquire about Scaling](https://www.scraperapi.com/?fp_ref=coupons) |
| Professional | 10,500,000 | 300 | Country-level | Quoted per tier | [Enquire about Professional](https://www.scraperapi.com/?fp_ref=coupons) |
| Advanced | 21,500,000 | 500 | Country-level | Quoted per tier | [Enquire about Advanced](https://www.scraperapi.com/?fp_ref=coupons) |
| Enterprise | 22,000,000+ | 500+ | Country-level | Custom | [Talk to sales about Enterprise](https://www.scraperapi.com/?fp_ref=coupons) |

Annual billing is a straight discount on the monthly rate: Startup costs $1,609.20 a year instead of $1,788, a saving of $178.80. Annual plans hand you the full year of credits upfront and let you spend them on your own schedule, which matters if your scraping is seasonal. Refunds are seven days, no questions asked, and you can cancel from the dashboard at no charge.

The three largest tiers are quoted per tier rather than published flat, so the credit, thread and geotargeting figures above are the parts you can plan against today.

## What a credit actually buys (and why 1,000,000 isn't 1,000,000 pages)

Every request is priced by what sits behind it. ScraperAPI publishes the multipliers, which is more than most hosted scraping APIs do, and it exposes the cost of any single request through the `sa-credit-cost` response header, a Domain Cost Estimator in the dashboard, and a `max_cost` parameter you can set per scrape.

| Request type | Credits |
| --- | --- |
| Standard page | 1 |
| E-commerce (Amazon, Walmart, eBay) | 5 |
| SERP (Google, Bing, including subdomains) | 25 |
| Social (LinkedIn) | 30 |
| Bot-protection bypass (Cloudflare, Cloudflare Turnstile, DataDome, PerimeterX/HUMAN, Akamai) | 10 |
| JavaScript rendering (`render=true`) | 10 |
| Premium proxies (`premium=true`) | 10 |
| Screenshot (`screenshot=true`) | 10 |
| Premium proxies + JS rendering | 25 |
| Ultra premium (`ultra_premium=true`, paid plans only) | 30 |
| Ultra premium + JS rendering | 75 |

Geotargeting, sessions, device type, output format and auto-parsing cost nothing extra. The multiplication is where a plan gets decided:

| Workload | Credits each | Hobby (100,000) | Startup (1,000,000) |
| --- | --- | --- | --- |
| Standard page | 1 | 100,000 requests | 1,000,000 requests |
| Amazon / Walmart / eBay | 5 | 20,000 | 200,000 |
| Google or Bing SERP | 25 | 4,000 | 40,000 |
| LinkedIn | 30 | 3,333 | 33,333 |
| Ultra premium + JS rendering | 75 | 1,333 | 13,333 |

At Startup, 1,000,000 credits for $149 works out to about $0.15 per 1,000 standard page requests. The same budget spent on Google SERPs is roughly $3.73 per 1,000 requests. That is not a pricing trick; it reflects the cost of getting past Google. But it does mean a team comparing "credits per dollar" across vendors is comparing numbers that were never equivalent.

One caveat on stacking: the published combinations (premium proxies plus JS rendering at 25, ultra premium plus JS at 75) are not always the sum of their parts, so don't hand-calculate a combined cost. Read the `sa-credit-cost` header on a real response instead, or estimate the URL first. 👉 [Create a free ScraperAPI key and price your target URLs](https://www.scraperapi.com/?fp_ref=coupons)

## Six ways to send a request, and the three most people end up using

The documentation lists six endpoints. They are not alternatives at the same level; they map to different jobs.

**1. The standard API endpoint.** `https://api.scraperapi.com` takes a URL plus your key and returns the page. This is the default for one-off requests and anything inside a normal request loop.

**2. The Async API endpoint.** `https://async.scraperapi.com` exists for volume. ScraperAPI's own description is "send millions of requests asynchronously", and if you are queuing six figures of URLs, synchronous calls will be the bottleneck long before credits are.

**3. The proxy port.** `http://scraperapi:APIKEY@proxy-server.scraperapi.com:8001` turns ScraperAPI into an HTTP proxy you can drop under an existing scraper. If your code already works and you only want the IP rotation and unblocking handled elsewhere, this is the smallest change you can make: swap the proxy address, keep your parsing.

**4. Structured Data Endpoints.** `https://api.scraperapi.com/structured/` returns parsed JSON or CSV for supported domains instead of full HTML. Useful, but limited, and covered in the next section.

**5. DataPipeline.** `https://datapipeline.scraperapi.com/api/projects` handles bulk jobs and scheduled runs. ScraperAPI describes it as automating collection "without writing a single line of code", which makes it the option for operations teams who need a recurring dataset rather than a scraping script.

**6. SDKs.** Available for some programming languages only. The documentation does not promise parity across languages, so confirm yours exists before you design around it.

Around those six sit the integrations: Crawler v2.0 for following links across a domain, an MCP server for wiring ScraperAPI into an LLM, a LangChain integration, a community n8n node for low-code automations, and an AI Parser still in closed beta that extracts structured data from almost any site using schema definitions.

## Two limits that will stop a project cold

### The 50MB request ceiling

ScraperAPI can fetch web pages, API endpoints, images, documents, PDFs and other files, and every request has to be authenticated with an API key. The constraint buried in the docs is a **50MB request size limit**. For HTML that is irrelevant. For a media-heavy target, a large PDF archive, or anything approaching video, it quietly rules ScraperAPI out for a slice of your list, and you find out at runtime rather than at checkout.

### Structured Data Endpoints cover nine scrapers on three domains

The SDE catalogue is: Amazon Search, Amazon Product, Amazon Offers, Google Search, Google Shopping, Google News, Google Jobs, Walmart Product, and Walmart Search. That is nine scrapers across Amazon, Google and Walmart, returning JSON or CSV with fields like name, price, rating and ranking position.

Anything outside those three domains has to go through the general endpoint and be parsed yourself. If your project is "monitor 40 retail sites", the structured-data advantage applies to a small fraction of your traffic and you should cost the plan as a plain fetch layer. If your project is Amazon ASIN monitoring or Google SERP tracking, SDE saves you a parser and a maintenance burden, and the 5-credit and 25-credit multipliers above are the real price of that convenience.

Worth knowing on the infrastructure side: ScraperAPI advertises a pool of more than 40 million proxies across 50+ countries, and states it is CCPA and GDPR compliant. The marketing line about never getting blocked again is a claim, not a service level. Enterprise adds a dedicated support team and a shared Slack channel, which is the part large teams actually notice during an incident.

## When you don't need a scraping API at all

Before pricing any scraping API, check whether your target already exposes the data through its own endpoints. Apify's documentation on API scraping defines the practice as locating a site's API endpoints and fetching from those directly, rather than parsing rendered HTML, and its list of advantages is a fair summary of when a paid unblocking layer is redundant:

- APIs change far less often than page structure and CSS selectors do, so maintenance drops.
- Most accept query parameters (`maxPosts`, `fromCountry`) that map straight onto your scraper's configuration.
- On dynamic sites you skip the headless browser, which is both faster and cheaper.
- Sending requests to an API is lighter on the target site than hammering rendered pages.

The cost is on your side: session cookies or API keys in headers, rate limits, GraphQL payloads, and responses that occasionally arrive Base64-encoded and need decoding before they are usable. If you can absorb that once for a stable target, you don't need to rent a fetch layer for it. If the target hides behind Cloudflare or rotates its markup weekly, that's exactly what you're paying ScraperAPI for.

## ScraperAPI vs Firecrawl vs Scrapy.io

The three products solve different problems, which is why price comparisons between them tend to be meaningless.

**Firecrawl** is built around output formats rather than around unblocking. Its scrape endpoint supports 18 formats, including Markdown, HTML, raw HTML, JSON, screenshots, links, images, change tracking and question-answering extraction. `onlyMainContent` defaults to on and is a deterministic HTML-level filter applied before Markdown is generated, with no LLM involved; a separate beta option, `onlyCleanContent`, runs an LLM pass to strip cookie banners, ads and breadcrumbs. Caching is a first-class feature: `maxAge` defaults to two days, and cache hits are claimed to speed scrapes up by 500%. PDFs bill at 1 credit per page, and lockdown mode (cache only, no outbound request) costs 5 credits on a hit and 1 on a miss. If your destination is a vector store or an LLM pipeline, that format-first design is closer to what you need than a credit multiplier for Amazon.

**Scrapy.io** sells finished scrapers rather than a general fetch layer. Its catalogue includes ready-made tools for Instagram, LinkedIn, Google Maps and TikTok, billing is per successful result (you pay for rows, not requests), one API key covers discovery, runs, datasets and schedules, and exports come as JSON, CSV or JSONL. It describes itself as an Apify alternative. Note that this is a hosted commercial platform and has nothing to do with the open-source Scrapy framework, despite the name. If you want someone else's LinkedIn scraper, Scrapy.io is the closer fit; if you want your own code to stop getting blocked, it isn't.

**Oxylabs** also offers a scraping endpoint that returns HTML or structured JSON with rendering and geo-location options, so it belongs on the same shortlist if you're collecting quotes.

Where ScraperAPI sits: it is the option for teams who own their parsing code and want a per-request-priced fetch layer under it, with published multipliers and a proxy port that takes minutes to adopt.

## FAQ

**Do I need an API key on every request?** Yes. All ScraperAPI requests must be authenticated with a key, including calls through the proxy port, where the key is embedded in the proxy URL.

**Can it fetch PDFs and images?** Yes, treated like any other URL, subject to the 50MB request limit.

**Synchronous or Async?** Sync for interactive work and small loops; Async once your queue is large enough that waiting on responses costs more than managing a job.

**Does DataPipeline require code?** ScraperAPI describes it as automating data collection without writing a single line of code, with scheduling for recurring jobs.

**What happens when credits run out?** By default scraping pauses until renewal. Smaller plans can enable auto-upgrade to move up a tier automatically; larger plans add pay-as-you-go at a fixed per-credit rate up to a monthly cap you set, and the cap is a spending limit rather than a prepayment.

**Is there a refund?** Seven days, no questions asked.

## How to pick a plan in one pass

Match the plan to the workload, not to the company size:

- Plain HTML pages at modest volume: free tier to test, then Hobby at $49 for 100,000 credits and 20 threads.
- Amazon, Walmart or eBay products in the hundreds of thousands: Startup's 1,000,000 credits buy 200,000 product requests, and the multiplier is what pushes you past Hobby.
- Google or Bing SERP tracking: 25 credits per request means 4,000 SERPs on Hobby and 40,000 on Startup. Size on that number.
- Country-level geotargeting outside the US and EU: Business at $299 is the first tier that offers it, whatever your credit volume.
- Multi-million-request pipelines with an on-call expectation: the larger tiers, where the Slack channel and dedicated support matter more than the per-credit rate.

Two things to confirm before you enter a card. First, run your actual target URLs through the Domain Cost Estimator and set `max_cost`, because a URL you assumed was 1 credit could be 25. Second, decide whether your workload needs structured endpoints or plain HTML, since that answer changes which tier is affordable rather than whether the product works.

The free tier plus a five-minute test on ten of your real URLs will answer both questions more reliably than any comparison table, including this one. 👉 [Open a free ScraperAPI account and size the plan from real numbers](https://www.scraperapi.com/?fp_ref=coupons)
