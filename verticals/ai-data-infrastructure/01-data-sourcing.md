# Layer 1: Data Sourcing

## What Goes In

The raw internet. Government documents. Medical records. Sensor logs from cars. Satellite imagery. Social media posts. Scientific papers. Basically, any digital information that can teach a pattern to a machine.

## What Comes Out

Structured, accessible collections of raw data ready for cleaning and labeling. Think of it as iron ore before it goes to the steel mill.

## Who Does It

### The Public Web Archivists
- **Common Crawl** — A non-profit that crawls the entire web and gives the data away for free. If you have ever used a large language model, it was probably trained on Common Crawl data. It is the foundation of modern AI, and almost nobody knows it exists.
- **Internet Archive** — The Wayback Machine's bigger, deeper cousin. Preserves web pages, books, and media for researchers and historians.

### The Commercial Scrapers
These companies turn the messy web into clean, structured data feeds:
- **Bright Data** — Formerly Luminati. Operates a massive proxy network that lets companies scrape websites at scale without getting blocked.
- **ScrapingBee** — A simpler API for developers who need to extract data without managing proxies themselves.
- **Apify** — A platform where developers build and share "scrapers" as ready-to-use tools. Like an app store for web crawling.

### The Data Marketplaces
Instead of scraping, you can buy data like you buy groceries:
- **Snowflake Data Marketplace** — Companies sell proprietary datasets inside Snowflake's data warehouse. You can query them without ever downloading a file.
- **AWS Data Exchange** — Amazon's version of the same idea. Subscribe to datasets and they appear directly in your AWS account.
- **Databricks Marketplace** — Focused on machine-learning-ready datasets, often pre-cleaned and documented.

### The Synthetic Data Builders
Why collect real data when you can generate fake data that is just as good?
- **Gretel** — Creates synthetic versions of sensitive datasets (like medical records) that keep the statistical patterns but remove personal information.
- **Hazy** — Similar idea, focused on enterprise compliance and privacy regulations.

## Key Trends

**The shift from "scrape everything" to "license specific datasets."** Early AI models were trained on whatever was publicly available. Now, lawsuits and quality concerns are pushing labs toward licensed, curated data. This is why Reddit, Twitter, and news publishers now charge for API access.

**Synthetic data is going mainstream.** In fields like healthcare and finance, real data is locked behind privacy laws. Synthetic data lets researchers train models without ever touching a real patient's record.

## The Stalwart

**Common Crawl** is the ultimate Stalwart. A small non-profit team maintains a dataset that powers billion-dollar language models. No marketing team. No venture funding. Just a crawler, some hard drives, and an open web.

---

*Next: [02-data-annotation](02-data-annotation.md)*
