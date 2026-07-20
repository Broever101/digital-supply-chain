# Layer 2: Virtualization and Cloud

## What Goes In

Physical GPUs, packaged into racks (HGX, DGX, NVL72). Megawatts of power. Network fabric. Capital — lots of it.

## What Comes Out

A slice of GPU compute, billed by the second, minute, hour, or token. Sold through three go-to-market shapes: hyperscaler instances, neo-cloud rentals, and sovereign/on-prem deployments.

This is the layer where physical hardware becomes a commercial product. Everything above — orchestration, engines, applications — assumes the units this layer produces.

## Who Does It

### The Hyperscaler AI Clouds

The Big Three operate the largest fleets, with the deepest enterprise integration. They are simultaneously the largest buyers of NVIDIA silicon and the largest builders of custom alternatives.

- **AWS** — **P5** and **P5e** instances (8× H100 / H200) are the workhorses. **P6** (8× B200) is rolling out. **Inf2** (Inferentia2) and **Trn2** (Trainium2, the "Project Rainier" cluster for Anthropic) for those willing to leave NVIDIA. **EFA** fabric up to 400 Gb/s. The deepest enterprise compliance and the broadest region coverage.
- **Azure** — **ND H100 v5** generally available. **NDv5 H200** rolling out. **ND GB200 v6** in preview. **Maia100** instances running parts of Copilot and Azure OpenAI inference. Tightest OpenAI integration via Azure OpenAI parity pricing.
- **Google Cloud** — **A3**, **A3 Mega**, **A3 Ultra** (H100 / H200), **A4** (B200, limited). Plus the entire **TPU lineage**: CT4/CT5/CT6 (Trillium)/CT7 (Ironwood)/CT8 (8t training, 8i inference). The only hyperscaler where you can run at scale on non-NVIDIA silicon and not feel it.

The hyperscalers win on compliance, billing consolidation, IAM integration, and breadth of adjacent services. They lose on price (typically 1.5–3× more expensive per GPU-hour than neo-clouds) and on the bleeding edge of NVIDIA hardware (CoreWeave shipped GB200 before any hyperscaler did).

### The Neo-Clouds — Purpose-Built AI Cloud

This is where the most interesting business stories are. These companies exist to do one thing: rent GPUs to AI workloads, cheaply and well.

- **CoreWeave** — The dominant neo-cloud. Ex-crypto miner (Atlantic Crypto, founded 2017). Kubernetes-native stack. First to cloud-deploy GB200 NVL72 in February 2025, first to ship GB300 commercially in July 2025. Public (Nasdaq: CRWV) since March 2025 — IPO raised $1.5 billion. 2025 revenue $5.13 billion, net loss $1.2 billion (the unit economics are still being proven). Customers: Microsoft (60%+ of revenue in 2024), OpenAI ($12B / 5-year deal, March 2025), Meta ($21B deal, April 2026), Anthropic (April 2026). 32 datacenters, 250,000 GPUs. Acquired Weights & Biases ($1.7B), OpenPipe, Monolith AI. Recognized as "Visionary" in Gartner Magic Quadrant for Cloud AI Infrastructure 2026 and won SemiAnalysis Platinum ClusterMAX twice. **Distinct innovation: a single-GPU inference pricing tier** ($6.16–$10.50/hr) — most GPU clouds force you to rent the whole 8-GPU node. Quietly serves inference buyers who don't need training capacity.
- **Lambda** (formerly Lambda Labs, now lambda.ai) — Started as a workstation builder for students. Now rents H100, H200, B200, A100, GH200 by the hour. A100 at $2.79/hr, H100 at $3.99–$4.29, B200 at $6.69–$6.99. The favorite of ML researchers and indie developers. Explicitly positions for sovereign/enterprise/government customers.
- **Crusoe Energy** — Flared-gas-powered datacenters. The structural innovation: pay nothing for the gas (it would have been flared anyway), sell the compute at market rates. Series D ~$686 million in 2024 at ~$2.5 billion valuation.
- **Together AI** — Inference API + GPU cloud + fine-tuning, strong on Llama-series hosting. Llama-3 API pioneer. Custom inference engine built on FlashAttention lineage (Tri Dao's team).
- **Nscale** — UK-based AI neocloud, $14.6 billion valuation March 2026. Aggressive European buildout. The European sovereign-AI play.
- **Genesis Cloud** — Germany-based, GDPR-friendly. Smaller but a real niche.
- **Vector Core Compute** — New (May 2026), launched by Vista Equity Partners as an "agentic neocloud" using SambaNova + Intel compute. The newest entrant.

### Serverless Inference Platforms

A subset of neo-clouds that bill per invocation (or per second of compute, with no idle commitment). These are the closest thing to "AWS Lambda for GPUs."

- **Modal** — Serverless-per-second. B300 at ~$7.10/hr, B200 at ~$6.25/hr, H100 at ~$3.95/hr, A100 at ~$2.50/hr. Bills per CPU cycle. Cheapest true-serverless for spiky workloads. Free academic credits up to $10k.
- **Baseten** — Series F (per banner on pricing page; dollar amount not primary-verified). Model APIs and dedicated instances (H100 $6.50/hr, B200 $9.98/hr, **no idle billing**). Customer list reads like the who's who of AI-native apps: Cursor, ClickUp, Decagon, Descript, Harvey, Notion, Patreon, Writer, Sourcegraph, Zed, Amp, Hubspot. Strong production-SLA focus. Publicly known NVIDIA Dynamo user (2× TTFT improvement).
- **Replicate** — Per-second billing for community + proprietary models. Powered by **Cog**, its open-source model packager. A100 80GB at $5.04/hr, H100 at $5.49/hr, 8× H100 at $43.92/hr.
- **Beam** (beam.cloud) — Open-sourced core runtime, Modal-clone DX, multi-cloud.

### Spot / Consumer-Grade Marketplaces

The cheapest GPUs in the world, with the SLAs to match.

- **RunPod** — Two-tier trust model: "Secure Cloud" (B300 $7.39/hr, H100 SXM $2.99/hr) and "Community Cloud" (H100 SXM $2.69/hr, A100 SXM $1.39/hr, **RTX 4090 $0.34/hr**). The Community tier is a marketplace of independent hosts. Lowest published GPU rates anywhere.
- **Vast.ai** — Similar marketplace positioning. Bootstrapped, popular with researchers and hobbyists.
- **TensorDock** — B2B-focused marketplace.

### Sovereign / On-Prem

For customers who cannot or will not send data to a public cloud: defense, finance, healthcare, regulated industries, and increasingly entire nations.

- **NVIDIA DGX Cloud / DGX OS** — NVIDIA's own managed cloud, running on partner infrastructure (CoreWeave, Oracle, etc.). DGX OS is a Linux distro optimized for NVIDIA hardware.
- **HPE Cray XD670 / EX** — HPC-heritage AI racks. HPE-Cerebras partnership for the Leibniz Supercomputing Center.
- **SuperMicro HGX systems** — High-volume OEM of HGX H100/H200/B200 baseboards.
- **Dell PowerEdge XE9712** — First enterprise Blackwell server racks (November 2024). Dell-CoreWeave infrastructure partnership.
- **Lenovo ThinkSystem SR680a V3** and similar.
- **Cisco AI racks** — Partnership with NVIDIA.

### The Sovereign AI Wave

A structurally distinct market that deserves its own callout. National governments and regulated industries are signing multi-year, multi-billion-dollar hardware leases to keep AI compute inside their borders.

- **G42** (UAE) — Major Cerebras customer.
- **MBZUAI** (UAE) — 62% of Cerebras's 2025 revenue. The customer-concentration risk is real.
- **PIF** (Saudi Arabia) — $1.5 billion committed to Groq for a Dammam datacenter (February 2025).
- **SambaNova sovereign partner network** — Four named providers: Argyll (UK), Infercom (EU), OVHcloud (EU), SouthernCrossAI (Australia).
- **JPMorgan Chase** — Selected SambaNova RDUs for on-prem inference (July 2026).

This is the dark-horse business model. None of the pure-software aggregators (OpenRouter, Together, Replicate) can play here. The sale is hardware-lease-shaped: multi-year contracts, data-residency hard requirements, regulated-customer compliance signatures.

## GPU Virtualization — How a GPU Becomes a Billable Unit

The economics of every cloud on this layer depend on how finely they can slice a GPU. NVIDIA's datacenter SKUs support hardware-level partitioning; consumer cards do not, by NVIDIA policy.

- **MIG (Multi-Instance GPU)** — Available on H100, H200, GB200. Hardware-level partitioning of one GPU into up to **7 isolated instances** (H100: 7×10GB; H200: 7×18GB). Each MIG instance has isolated memory, cache, and compute. True hardware isolation, not time-slicing. **Unavailable on consumer GeForce cards by NVIDIA policy** — NVIDIA artificially segments this feature to datacenter SKUs.
- **vGPU (Broadcom/VMware, Nutanix)** — Hypervisor-level GPU sharing, mostly for VDI and general workloads. NVIDIA vGPU (formerly GRID) is the dominant implementation.
- **Time-slicing** — Software round-robin between contexts on a GPU. Lowest overhead, lowest isolation. Default on most cloud "fractional GPU" offerings. Causes noisy-neighbor problems.
- **Run:ai** — Acquired by NVIDIA in April 2024 for ~$700 million. Kubernetes-native GPU scheduler. Fractional GPU, multi-tenancy, gang scheduling, fair-share. NVIDIA rebranded it as "NVIDIA Run:ai" and open-sourced parts. The de-facto standard for AI cluster sharing.

## The Utilization Problem

Idle GPUs are pure cost. Industry estimates of typical cluster utilization are **30–50% without smart scheduling** — the entire reason Run:ai exists. Hyperscalers achieve higher (60–75%) via massive workload diversity. Neo-clouds often run 40–55%.

This is the central economic fact of this layer. A GPU sitting idle costs the same as a GPU running hot. Whoever can push utilization from 40% to 70% without violating latency SLAs captures the margin. This is why continuous batching, prefix caching, and disaggregated prefill/decode (covered in Layer 4) matter so much — they are utilization improvers, not just latency improvers.

## Billing Models

- **Per-GPU-hour** — The most common model. $1.50–$4 for H100, $5–$8 for B200, sub-$1 for consumer cards. Reserved commits typically 30–70% off.
- **Per-token** — Vendors like Together AI, OpenRouter, Baseten, DeepInfra bill by input/output token. Typically $0.10–$5 per million tokens depending on model.
- **Per-second** — Modal and Replicate. The most granular. No idle commitment at all.
- **Reserved vs On-Demand** — 1-month to 3-year commits for substantial discounts.
- **Service tiers** — Standard / Priority (+75%) / Flex (−50%) / Reserved. Now an industry standard across AWS Bedrock, DeepInfra, and Anthropic. Pure margin optimization that did not exist in 2023.

## Key Trends

**The hyperscaler squeeze.** AWS Bedrock carries 18 model providers, including direct competitors. Azure Foundry resells Fireworks. The hyperscalers are simultaneously competing with aggregators and reselling them. Classic cloud-platform behavior: own the billing relationship, the compliance signature, and the customer data, and let the ISVs fight for the rest.

**The single-GPU inference tier is a real innovation.** Most GPU clouds force you to rent the whole 8-GPU node — great for training, terrible for inference workloads that only need one GPU. CoreWeave's $6.16/hr single-GPU H100 inference pricing is the most under-discussed pricing innovation in the market.

**Neo-cloud margin sustainability is an open question.** CoreWeave's 2025 net loss of $1.2 billion against $5.13 billion revenue is real money losing. Customer concentration (Microsoft 60%+ in 2024) is structural risk. The $100B revenue backlog looks great, but backlogs are not revenue.

**Sovereign AI is the only segment immune to commoditization.** Nineteen providers can serve `gpt-oss-120b` to a developer in San Francisco. Exactly zero of them can serve a regulated workload to a Saudi government ministry. The sovereign segment will keep growing as EU AI Act enforcement matures.

**GPU virtualization is the unsexy battleground.** NVIDIA's MIG limitation to datacenter SKUs is a deliberate margin-protection move. Run:ai is now NVIDIA's. Watch for open-source alternatives (JuiceLabs and similar) that try to break the lock.

## The Stalwart

**RunPod Community Cloud** is the Stalwart of this layer. A two-tier trust model: Secure Cloud for production, Community Cloud for everyone else. RTX 4090s at $0.34/hour. A100 SXMs at $1.39/hour. The lowest published GPU rates in the world, sustained by a marketplace of independent hosts. The structural analog to Vast.ai but with better UX. Most AI infrastructure discourse ignores this tier entirely — it is where grad students, indie developers, and hobbyists actually get their first GPU hours. The gateway drug to the entire AI ecosystem.

---

*Next: [04-orchestration-layer](04-orchestration-layer.md)*
