# Sovereign Deployments — Real-World Case Studies

## What Goes In

A country, a regulated industry, or a large enterprise that cannot send data to a US hyperscaler. Capital. A stack to deploy.

## What Comes Out

A working AI datacenter, owned and operated by the sovereign entity, with the political and operational characteristics that entails.

This file maps the actual reference architectures and named sovereign deployments that exist in 2026. It is shorter on theory and longer on real customers, real dollar amounts, and real geopolitical constraints.

## The Reference Architecture Taxonomy

There are five patterns sovereign customers actually buy:

### 1. NVIDIA DSX — Full AI Factory

NVIDIA's 2026 platform. DSX Reference Design + DSX Sim + DSX OS + DSX MaxLPS + DSX Flex + DSX Exchange. Partners: IREN (5 GW), Jacobs, Phaidra, Schneider/EcoDataCenter, Emerald AI. This is the canonical sovereign-AI reference architecture for buyers who want one throat to choke.

**Lock-in**: high. The entire stack — from facility design through DGX OS to inference engines — is NVIDIA.

### 2. NVIDIA AI Enterprise — The Commercial Bundle

The commercial license that unlocks vGPU, Run:ai, DOCA, GPU/Network/DPU Operators, Container Toolkit, NeMo model-building framework. Current: Infra 8.1 (R595) and Infra 7.7 LTSB (R580). The choice for enterprises that want NVIDIA-supported software without committing to the full DSX facility play.

### 3. Red Hat OpenShift AI

IBM/Red Hat's enterprise AI platform on OpenShift. Multi-vendor hardware (NVIDIA, AMD, Intel). Includes Open Data Hub, RHODS. The choice for enterprises that want to avoid full NVIDIA lock-in and are already invested in Red Hat.

### 4. SambaNova Sovereign AI

The strongest publicly-disclosed turnkey sovereign pattern. Four named partners:

- **OVHcloud** (EU)
- **Argyll DataCloud** (UK)
- **Infercom** (EU)
- **SouthernCrossAI** (Australia)

90-day deployment claim. SambaRack ~10 kW/rack **air-cooled** (a real differentiator vs Blackwell's liquid-cooling requirement). $1B first close at $11B valuation (2026). The only turnkey sovereign vendor with multiple named reference customers.

### 5. Roll-Your-Own OSS

Talos Linux + MAAS + Cilium + KAI Scheduler + Dynamo + vLLM + VictoriaMetrics + Vault + Harbor. Low lock-in but you own the integration burden. The choice for sovereign customers with strong engineering teams (national labs, telcos, defense ministries).

## Named Sovereign Deployments

The actual customers that have announced sovereign AI deployments.

### UAE

- **G42** — major Cerebras customer. Wikipedia 2025: 62% of Cerebras's 2025 revenue was MBZUAI (the Mohamed bin Zayed University of Artificial Intelligence in UAE). Customer concentration risk is real.
- **MBZUAI** — uses Cerebras for research and teaching.
- **G42 → Microsoft pivot** (2024): Microsoft invested $1.5B in G42; G42 agreed to divest from Chinese hardware (Huawei, ZTE) and adopt US tech stack. The template for how a GCC state gets US GPU access — *political divestment from China as the price of admission*. (Widely reported Reuters/FT/Bloomberg; the specific terms are MED confidence.)

### Saudi Arabia

- **Saudi PIF + Groq Dammam**: $1.5B deal, February 2025 (Reuters). Largest disclosed non-NVIDIA sovereign inference deal.
- **PIF / HUMAIN AI initiative** (2024-2025): public commitments to both NVIDIA (large H100 orders, prospective GB300 clusters) and Huawei (5G, some Ascend pilots).
- **ARAMCO** AI infrastructure investments.

### United Kingdom / EU

- **Argyll DataCloud** (UK) — SambaNova sovereign partner.
- **Infercom** (EU) — SambaNova sovereign partner.
- **OVHcloud** (EU/France) — SambaNova sovereign partner. Independent European cloud.

### Australia

- **SouthernCrossAI** — SambaNova sovereign partner.

### United States (Corporate Sovereign)

- **JPMorgan Chase** — Selected SambaNova RDUs for on-prem inference (July 2026). A US bank running sovereign AI infrastructure for regulatory and IP-protection reasons.
- **Apple Private Cloud Compute** — Apple Foundation Models run on NVIDIA Confidential Computing on Google Cloud. The canonical "corporate-sovereign without owning a datacenter" pattern. Apple gets the cost benefits of public cloud while keeping user data cryptographically isolated from Google.

### India

- **India AI Mission** — $1.2B+ sovereign compute program, 34,000+ GPU target. Multi-year national effort.

### Other Signals (Lower Confidence)

- **Indonesia, Vietnam, Malaysia** — national AI programs in early stages.
- **Russia** — reported to be using Chinese silicon (UNVERIFIED, LOW confidence).
- **Africa** — DeepSeek's Wikipedia entry notes explicit Africa expansion alongside Huawei cloud — Chinese "sovereign AI" diplomacy in the global south.

## Why Sovereign AI Is Structurally Different

The buyer is not a developer. The buyer is a national government, a regulated industry, or a sovereign wealth fund. The sale is shaped like a multi-year hardware lease, not a SaaS subscription. The decision criteria include:

- **Data residency** — hard legal requirement.
- **Compliance signatures** — HIPAA, FedRAMP, GDPR, EU AI Act, GCC-specific regulations.
- **Multi-year amortization** — capex models, not opex.
- **Political alignment** — US vs Chinese tech stacks have different political conditions (see [Chinese hardware chapter](../07-chinese-hardware/README.md)).
- **Workforce development** — sovereign deals usually include training and knowledge transfer.

None of the pure-software aggregators (OpenRouter, Together, Replicate) can play in this market. The sovereign segment is structurally separate.

## The Geopolitical Context — US Export Controls

The four waves of US BIS export controls (covered in detail in [07-chinese-hardware/README.md](../07-chinese-hardware/README.md)) have reshaped the sovereign AI market:

- **Oct 2022** — block A100/H100 to China; NVIDIA responds with A800/H800 (fuse-blown interconnect).
- **Oct 2023** — close the A800/H800 loophole; add Biren, Moore Threads, Cambricon to Entity List.
- **Jan 2025** — DeepSeek R1 release; Trump inherits Biden controls.
- **Apr 2025** — H20 banned from China; Huawei announces Ascend 920 as H20-class replacement. **Inflection point.**

For sovereign customers in the GCC, India, Pakistan, Africa, and Southeast Asia, the strategic question is: do we buy US (with political conditions and quota risk) or Chinese (without conditions, but with software gaps)? Most are hedging by buying both.

## Stalwart Opportunities

The biggest gaps in the sovereign AI market — where small teams could build defensible products:

1. **Open-source sovereign cloud distribution** — no widely-adopted turnkey ISO exists. Whoever builds "Talos + MAAS + Cilium + KAI + Dynamo + vLLM as a single bootable image" becomes the Ubuntu of sovereign AI.
2. **Multi-vendor GPU scheduling** — NVIDIA + AMD + Ascend + Cerebras in one pool. Run:ai is NVIDIA-only; HAMi is China-focused. There is no neutral third option.
3. **OSS fabric manager for UEC** — no widely-adopted open-source equivalent to NVIDIA UFM exists.
4. **Inference-specific storage tier** — most "AI storage" optimizes for training checkpoints, not cold-start. The Tensorizer / ModelExpress pattern is right but generic.
5. **Workload-aware cost attribution** — token + GPU-hour + power-tariff in one place. Kubecost is too generic; metering systems are too token-focused.
6. **Air-gapped update infrastructure** — TUF + Sigstore + local mirrors, turnkey.
7. **Model-weight attestation** (SBOM for AI) — EQTY Lab is the only serious player. Becomes first-order as AI regulation matures.
8. **Cross-vendor observability** — heterogeneous NVIDIA/Ascend fleets need unified dashboards. Datadog, Grafana, Prometheus all assume NVIDIA today.

## The Stalwart

The Stalwart of sovereign AI is **SambaNova's sovereign partner network** — four named national/regional providers (Argyll, Infercom, OVHcloud, SouthernCrossAI) plus the JPMorgan on-prem deployment. The only vendor that has actually delivered turnkey sovereign AI infrastructure to multiple customers at scale. Whether SambaNova's technology is the best is debatable; their sovereign go-to-market execution is not.

---

*Next layer: [04-orchestration](../04-orchestration/README.md)*
