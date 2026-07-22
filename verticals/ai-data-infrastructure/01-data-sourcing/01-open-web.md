# 1. Open Web / Internet Data

## What Goes In

The raw, unfiltered internet. Web pages, blog posts, forums, code repositories, social media posts, and any text or media publicly accessible without a login.

## What Comes Out

Structured text corpora — billions of words in standardized formats — ready for language model pre-training. Also: image datasets, code datasets, and conversation logs.

## Key Players

| Name | URL | Role |
|------|-----|------|
| Common Crawl | https://commoncrawl.org | Non-profit that crawls the entire web and gives data away for free |
| C4 (Colossal Clean Crawled Corpus) | https://www.tensorflow.org/datasets/catalog/c4 | Google's cleaned version of Common Crawl |
| RefinedWeb | https://huggingface.co/datasets/tiiuae/falcon-refinedweb | TII's filtered and deduplicated web corpus |
| WebText | https://github.com/openai/gpt-2-output-dataset | OpenAI's original GPT-2 training dataset |
| Bright Data | https://brightdata.com | Commercial proxy network for large-scale scraping |
| Apify | https://apify.com | Marketplace of ready-to-use web crawlers |
| Zyte | https://www.zyte.com | Enterprise web scraping infrastructure |

## Open vs. Proprietary

Most foundational web corpora (Common Crawl, C4, RefinedWeb) are **open and free**. The tooling to scrape, clean, and filter them is increasingly **proprietary** and expensive.

## The Stalwart

**Common Crawl**. A tiny non-profit team maintains a dataset that powers billion-dollar language models. No marketing team. No venture funding. Just a crawler, hard drives, and an open web. It is the oxygen of modern AI — everywhere, essential, and almost invisible.

## TBD

- Deduplication techniques (MinHash, near-deduplication at scale)
- Quality filtering heuristics (perplexity scoring, language identification)
- Legal landscape: robots.txt, terms of service lawsuits, opt-out mechanisms
- Emerging alternative: licensed web data (Reddit, Twitter/X API pricing)

---

*Back to [overview](00-overview.md)*
