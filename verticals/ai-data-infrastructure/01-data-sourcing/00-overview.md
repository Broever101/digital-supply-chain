# Data Sourcing: The Lay of the Land

This is the entry point of the AI supply chain. Before any model can learn, someone must decide where the raw material comes from, how to extract it, and who controls access to it.

## The Taxonomy

```
DATA SOURCING FOR AI
│
├── 1. OPEN WEB / INTERNET DATA
│   ├── Raw web crawl archives
│   ├── Firehose APIs (social platforms)
│   ├── Specialized open datasets
│   └── User-generated content scrapes
│
├── 2. PROPRIETARY / COMMERCIAL DATA MARKETPLACES
│   ├── Cloud-native marketplaces (Snowflake, AWS, Databricks)
│   ├── Industry data vendors (Bloomberg, Reuters)
│   └── B2B data brokers (credit, firmographics, intent)
│
├── 3. SYNTHETIC / SIMULATED DATA
│   ├── AI-generated synthetic tabular/text data
│   ├── 3D simulation engines (autonomous, robotics)
│   └── Physics-based simulators
│
├── 4. SATELLITE & GEOSPATIAL
│   ├── Commercial satellite imagery
│   ├── Government/open satellite programs
│   └── Aerial / drone data
│
├── 5. SENSOR & IoT / MACHINE-GENERATED
│   ├── Autonomous vehicle sensors (LiDAR, radar, camera)
│   ├── Industrial IoT telemetry
│   └── Wearables / biometric streams
│
├── 6. SCIENTIFIC & ACADEMIC
│   ├── Biomedical (genomics, proteomics, imaging)
│   ├── Physics / astronomy
│   └── Social science / behavioral datasets
│
├── 7. GOVERNMENT / PUBLIC RECORDS
│   ├── Census & demographic
│   ├── Regulatory filings (SEC, patents)
│   └── Weather / climate / environmental
│
├── 8. MEDICAL / HEALTHCARE
│   ├── EHR / claims data (de-identified)
│   ├── Medical imaging archives
│   └── Clinical trial / real-world evidence
│
├── 9. FINANCIAL / MARKET DATA
│   ├── Exchange data & tick-by-tick feeds
│   ├── Alternative data (credit cards, foot traffic, web traffic)
│   └── Macroeconomic indicators
│
├── 10. HUMAN BEHAVIORAL / TELEMETRY
│   ├── App / web clickstream
│   ├── Gaming telemetry
│   └── Voice / conversational data
│
├── 11. MULTIMEDIA / CONTENT LIBRARIES
│   ├── Stock image / video / audio libraries
│   ├── News / media archives
│   └── Licensed creative content
│
└── 12. DOMAIN-SPECIFIC / VERTICAL DATA
    ├── Legal (case law, contracts, dockets)
    ├── Real estate (listings, transactions, valuations)
    └── Agriculture, energy, logistics, etc.
```

## The Hidden Layer — Brokers, Clean Rooms, and Privacy Tech

Most people assume AI labs get data directly from the source. They do not. There is an entire infrastructure layer sitting between the raw source and the model:

| Role | What They Do | Examples |
|------|--------------|----------|
| **Data Brokers** | Buy, clean, aggregate, and resell data across categories | Acxiom, LexisNexis, Experian, LiveRamp |
| **Data Clean Rooms** | Let two parties match datasets without exposing raw data | Snowflake Clean Rooms, AWS Clean Rooms, InfoSum, Habu |
| **Data Unions / Cooperatives** | Pool data from multiple sources for collective bargaining | Pool Data, Data Dividend Project |
| **Privacy Tech** | Make sensitive data usable for AI while compliant | Privitar, Immuta, BigID, OneTrust |

A satellite company sells to a geospatial analytics firm, which sells to a data broker, which feeds into a Snowflake marketplace, which an AI lab queries. The "source" is rarely direct.

## How AI Labs Actually Source (Three Real Pipelines)

### Pipeline 1: Web Text for LLM Pre-training
- **Raw**: The entire internet
- **Collector**: Common Crawl (non-profit), C4, RefinedWeb
- **Refiner**: Deduplication (MinHash), quality filtering, language ID
- **Aggregator**: Hugging Face Datasets, Together AI's RedPajama, AI2's Dolma
- **AI Lab**: Direct download or licensed access

### Pipeline 2: Satellite Imagery for Geospatial AI
- **Raw**: Planet Labs, Maxar, Sentinel-2 (ESA)
- **Collector**: UP42, SkyWatch (satellite imagery marketplaces)
- **Refiner**: Orbital Insight, Descartes Labs (analytics on raw pixels)
- **Aggregator**: AWS Open Data (Sentinel), Google Earth Engine
- **AI Lab**: Google, Microsoft FarmBeats, climate AI startups

### Pipeline 3: Medical Imaging for Diagnostic AI
- **Raw**: Hospital PACS systems, NIH archives (TCIA, ChestX-ray14)
- **Collector**: Flywheel, Centaur Labs (data curation from hospitals)
- **Refiner**: Annotation by radiologists (via iMerit, MD.ai)
- **Aggregator**: NVIDIA Clara (federated learning), hospital consortia
- **AI Lab**: Google Health, Aidoc, Tempus

## Quick Reference: Key Players by Category

| Category | Key Companies / Projects | Open vs. Proprietary |
|----------|--------------------------|---------------------|
| **Web Crawl** | Common Crawl, C4, RefinedWeb, WebText | Mostly open |
| **Web Scraping APIs** | Bright Data, ScrapingBee, Apify, Zyte | Proprietary |
| **Social Firehose** | X/Twitter API, Reddit API, Discord | Proprietary, expensive |
| **Data Marketplaces** | Snowflake, AWS Data Exchange, Databricks | Proprietary |
| **Synthetic Data** | Gretel, Mostly AI, Synthesis AI, Parallel Domain | Mixed |
| **Satellite** | Planet Labs, Maxar, BlackSky, Spire, Sentinel | Mixed |
| **Medical Imaging** | TCIA (NIH), Flywheel, MD.ai, NVIDIA Clara | Mixed |
| **Financial** | Bloomberg, Refinitiv, IEX Cloud, Polygon.io, Quandl | Proprietary |
| **Legal** | Casetext, vLex, Fastcase | Proprietary |
| **Scientific** | PubMed, arXiv, GenBank, PDB, Hugging Face Datasets | Mostly open |

---

## Deep Dives

- [01: Open Web / Internet Data](01-open-web.md)
- [02: Commercial Marketplaces](02-commercial-marketplaces.md)
- [03: Synthetic Data](03-synthetic-data.md)
- [04: Satellite & Geospatial](04-satellite-geospatial.md)
- [05: Sensor & IoT](05-sensor-iot.md)
- [06: Scientific & Academic](06-scientific-academic.md)
- [07: Government Records](07-government-records.md)
- [08: Medical & Healthcare](08-medical-healthcare.md)
- [09: Financial Data](09-financial-data.md)
- [10: Human Behavioral](10-human-behavioral.md)
- [11: Multimedia Libraries](11-multimedia-libraries.md)
- [12: Domain-Specific](12-domain-specific.md)
