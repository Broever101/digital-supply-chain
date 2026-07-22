# Digital Commodities Atlas

## The Thesis

Everyone wants to build the next Uber, the next Amazon, the next Facebook. But here is what nobody tells you: **Uber runs on Kafka, Redis, Twilio, Stripe, and Cloudflare.** The real unicorn potential is not always in the app layer. It is in the infrastructure that 1,000 future Ubers will need.

This project maps the **digital substrate** of industries. Not the glossy front-end apps, but the picks, shovels, and pipelines that make everything else possible.

### The Stalwart Moment

I discovered Stalwart, a mail server written in Rust, that handles IMAP connections at a latency most enterprise stacks cannot match. It was built by a small team, barely marketed, yet outperforms bloated legacy software. This made me realize: **every industry has its Stalwarts.** Tools that are precise, well-built, and hidden in plain sight.

## What This Repository Is

A living map of software supply chains across industries. Each "vertical" is an end-to-end look at how digital commodities flow from raw input to final output.

## Current Verticals

- **AI Data Infrastructure** (in progress) — How AI labs get, clean, label, and deliver data to training clusters. Data sourcing covers 13 categories: open web, commercial marketplaces, synthetic data, satellite/geospatial, IoT/sensor, scientific, government records, medical, financial, behavioral, multimedia, domain-specific, and cybersecurity. Data annotation covers multiple modalities (text/NLP, vision, audio, 3D/spatial) and domain specializations (autonomous vehicles, medical), plus the tooling layer.
- **AI Inference** (in progress) — The supply chain behind every chat reply, agent step, and AI-coded line: from silicon to API gateway. Covers hardware (NVIDIA, AMD, Groq, Cerebras), virtualization/cloud framed as a sovereign-cloud decision tree, orchestration patterns (Kubernetes, Ray, NVIDIA Dynamo, Mooncake), inference engines (vLLM, SGLang, llama.cpp), the application/control-plane layer (LiteLLM, OpenRouter, billing, observability), and a dedicated chapter on Chinese hardware (Huawei Ascend, Cambricon, Biren, etc.) and the open-source stack that runs on it — for the PK/GCC founder angle.

## How to Navigate

Each vertical follows the same structure, with depth scaled to the complexity of the supply chain:
- `00-executive-summary.md` — Why this matters, in plain language
- Numbered chapters (`01-*.md` and up) — The supply chain layers, from raw material to finished product. Deeper verticals (like AI Inference) use sub-folders for layers with many distinct technical approaches.
- `diagram.mmd` — A visual flowchart of the entire chain
- `players.json` — A structured database of every company and tool mentioned

## Who This Is For

Young builders, founders, and curious minds who want to understand the **underbelly** of technology. You do not need a CS degree. You need curiosity and the willingness to look where others do not.

## Contributing

This is a solo research project for now, but if you stumble upon a hidden gem — a Stalwart in your domain — open an issue or reach out.

## License

TBD. For now, this is private research.
