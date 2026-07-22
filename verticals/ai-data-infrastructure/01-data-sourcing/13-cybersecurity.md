# 13. Cybersecurity / Threat Intelligence

## What Goes In

Malware binaries, network traffic captures, phishing URLs, vulnerability disclosures (CVEs), dark web forum chatter, intrusion detection logs, fraud transaction records, and attacker infrastructure fingerprints. The raw material of digital conflict.

## What Comes Out

Labeled datasets that teach models to detect vulnerabilities, classify phishing attempts, identify malware behavior, map attacker tactics (MITRE ATT&CK), and predict exploit likelihood. The kind of data that trains an AI to think like a security analyst.

## The Market Gap

Here is the surprising finding: **no boutique vendor currently sells curated, labeled cybersecurity datasets to AI labs for model training.**

The cybersecurity data market is massive — threat intelligence alone is a multi-billion-dollar industry — but it serves defenders, not model trainers. The vendors below sell to SOC teams, government agencies, and fraud prevention departments. None of them have publicly positioned themselves as training data suppliers for frontier AI models.

## Key Players (Who Exist, But Do Not Serve AI Labs)

| Name | URL | Role | Why They Do Not Sell Training Data |
|------|-----|------|-----------------------------------|
| **Recorded Future** | https://www.recordedfuture.com | Threat intelligence platform aggregating 1M+ sources | Sells intel feeds to SOCs, not labeled datasets for LLM training |
| **Flashpoint** | https://flashpoint.io | Dark web intelligence, fraud data, credential leaks | Sells access to adversary spaces, not structured ML training corpora |
| **Intel 471** | https://intel471.com | Cyber HUMINT + underground marketplace monitoring | Human intelligence reports, not bulk labeled data for model ingestion |
| **Anomali** | https://www.anomali.com | Threat intelligence platform + security data lake | Enterprise security workflows, not AI lab data licensing |
| **GreyNoise** | https://www.greynoise.io | Internet noise classification (malicious vs benign scanning) | Labels traffic in real-time for SOCs, not as static training datasets |
| **EclecticIQ** | https://eclecticiq.com | European threat intel platform (STIX/TAXII normalization) | Standardizes intel sharing, not AI training data curation |
| **Mandiant (Google)** | https://www.mandiant.com | Incident response + frontline threat research | Internal Google SecOps use, not external data sales for model training |
| **VirusTotal** | https://virustotal.com | Crowdsourced malware labeling from 70+ AV engines | Google-owned; free community tool, not a commercial training data vendor |
| **PhishTank** | https://phishtank.org | Community-driven phishing URL verification | Operated by Cisco Talos; free, not sold as training data |

## Where AI Labs Actually Get Security Data

Since they cannot buy it from boutiques, frontier AI labs source cybersecurity knowledge through:

### 1. Public Web Scraping
- CVE databases (NIST NVD), security blogs, StackOverflow, GitHub repositories
- Academic papers from arXiv, IEEE, USENIX
- Vendor advisories (Microsoft, Cisco, Adobe security bulletins)
- **Problem**: Unstructured, inconsistent formatting, no quality guarantees

### 2. Academic Datasets
| Dataset | Source | Size | Limitation |
|---------|--------|------|------------|
| **CICIDS2017/2018** | Canadian Institute for Cybersecurity | 10 billion labeled flows | Network intrusion only, not code vulnerability or phishing |
| **UNSW-NB15** | UNSW Canberra | ~2M records | Modern attack patterns but narrow scope |
| **NSL-KDD** | University of New Brunswick | ~148K records | Classic but outdated (1999 KDD Cup derivative) |
| **CSE-CIC-IDS2018** | Canadian Institute for Cybersecurity | 10B labeled flows | Large but synthetic, not real enterprise traffic |

### 3. Internal Red Teams
- OpenAI, Anthropic, Google DeepMind maintain internal security teams
- They generate adversarial test cases, jailbreak attempts, and vulnerability scenarios
- **Problem**: Expensive, slow, limited to each company's specific threat model

### 4. Synthetic Generation
- Labs generate their own phishing emails, malware descriptions, and vulnerability reports
- **Problem**: Synthetic data may miss real-world adversarial nuances; mode collapse in attack patterns

## The Hidden Landscape: Where Data Is Trapped

| Source Type | Examples | Accessibility |
|-------------|----------|---------------|
| **Proprietary security company silos** | CrowdStrike, Palo Alto Networks, Fortinet | Locked behind enterprise contracts, never sold as raw data |
| **Government classified feeds** | NSA, GCHQ, CISA | Classified or restricted to cleared partners |
| **Financial fraud networks** | Banks, payment processors | PCI-DSS and privacy regulations prevent sharing |
| **Enterprise SOC logs** | Fortune 500 security operations | GDPR, HIPAA, and trade secret barriers |
| **Dark web monitoring** | Flashpoint, Intel 471 | Human-readable reports, not machine-parseable training corpora |

## Why the Gap Exists

1. **Regulatory friction**: Security logs contain personal data, financial records, and classified information. Labeling them for public sale is legally complex.

2. **Adversarial dynamics**: Unlike images of cats, malware samples and exploit techniques evolve daily. A labeled dataset from 2023 is partially obsolete by 2024.

3. **Vendor business models**: Threat intel companies sell *analysis* and *alerts*, not raw data. Their margin is in human expert interpretation, not bulk data licensing.

4. **No standardized format**: Unlike COCO for images or GLUE for NLP, cybersecurity lacks a universal labeled dataset standard that AI labs could consume off-the-shelf.

## The Underdog Opportunity

The "Scale AI of cybersecurity data" does not yet exist. A boutique could dominate by:

- **Curating CVE-to-code mappings**: Link every vulnerability disclosure to the actual code diff, creating labeled pairs of (vulnerable code, patch) for code-security models
- **Phishing corpora at scale**: Continuously collect, verify, and label phishing URLs vs legitimate sites, updated daily for model retraining
- **MITRE ATT&CK labeled narratives**: Transform threat intel reports into structured (text, TTP label) pairs for models to learn attacker behavior classification
- **Synthetic + real hybrid logs**: Generate realistic network traffic with injected attack patterns, labeled by attack stage (reconnaissance, exploitation, exfiltration)
- **Adversarial prompt datasets**: Curate jailbreak attempts, prompt injection samples, and security policy violations for alignment training

## The Stalwart

**PhishTank** (https://phishtank.org). A small community project run by Cisco Talos that has collected and verified millions of phishing URLs since 2006, completely free. No venture funding, no sales team, no AI lab contracts — yet it is one of the most important labeled datasets for training phishing detection models. If someone packaged PhishTank's data into a commercial, continuously updated, API-accessible training corpus, they would own a niche with no current competition.

## TBD

- Is anyone building a "Hugging Face Datasets for security"? (Not yet commercially)
- Will CISA or NIST create open security datasets for AI training? (Conceptual, not executed)
- Can federated learning unlock enterprise SOC data without centralizing it?
- The rise of AI-generated vulnerabilities: can synthetic CVEs supplement real disclosure data?
- Regulatory frameworks for security data licensing (EU AI Act implications)

---

*Back to [overview](00-overview.md)*
