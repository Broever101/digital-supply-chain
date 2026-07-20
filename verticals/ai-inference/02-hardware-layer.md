# Layer 1: Hardware

## What Goes In

Silicon wafers. Photolithography. HBM3e memory stacks from SK Hynix, Samsung, and Micron. Substrates from TSMC. Cooling infrastructure. Megawatts of electricity, increasingly from nuclear or behind-the-meter sources.

## What Comes Out

A chip — usually a GPU, sometimes an ASIC — that can hold a trained model in memory and emit tokens when asked. Packaged into systems (HGX, DGX, NVL72 racks) that hyperscalers and neo-clouds buy by the thousand.

This is the foundation of the entire stack. Every layer above is a workaround for, or an optimization of, what this layer makes physically possible.

## Who Does It

### NVIDIA — The Incumbent

NVIDIA does not just lead this layer; it has shaped it. Every serious AI workload in the world runs on NVIDIA silicon or runs in conversation with it.

- **H100 SXM5** (Hopper, 2022) — 80GB HBM3, 3.35 TB/s bandwidth, ~4 PFLOPS FP8. The chip that defined the 2023–24 boom. Still the workhorse of the industry.
- **H200** (Hopper, 2024) — 141GB HBM3e, 4.8 TB/s. Same compute, more memory. China blocked H200 imports in January 2026; the US cleared sales to ten Chinese firms (Alibaba, Tencent, ByteDance, JD, Lenovo, Foxconn) in May 2026.
- **H200 NVL** — PCIe dual-slot air-cooled version for mainstream enterprise. 600W.
- **GH200 Grace+Hopper Superchip** — 72 ARM Neoverse V2 cores alongside a Hopper GPU, coherent over NVLink-C2C. The first "superchip."
- **B100 / B200** (Blackwell, 2024–25) — 192GB HBM3e, 8 TB/s bandwidth, 4.5 PFLOPS FP8. B200 is 1,000W. Two-die design. The flagship Blackwell.
- **GB200 NVL72 rack** — The liquid-cooled slab. 36 Grace CPUs + 72 Blackwell GPUs in a single NVLink domain. 13.4 TB of HBM3e aggregate, 1,440 PFLOPS NVFP4 (sparse). Roughly $3 million per rack. CoreWeave was first to cloud-deploy it in February 2025.
- **GB300 NVL72** (Blackwell Ultra, 2025) — Now shipping commercially via Dell and CoreWeave. NVIDIA claims "up to 50× AI factory output vs Hopper," a number that needs workload-specific verification.
- **Rubin R100** (target H2 2026) — HBM4, TSMC 3N, 338 billion transistors, 2,300W TDP. Will require major facility redesigns.

**Why NVIDIA wins.** Three reasons compound. First, **CUDA** — the software stack every ML framework targets. Second, **NVLink** — the interconnect that lets multiple GPUs behave as one (5.0 generation hits 1.8 TB/s on Blackwell). Third, **HGX/DGX/MGX** — the reference designs that every OEM (SuperMicro, Dell, HPE, Lenovo, Cisco) builds to. NVIDIA owns the entire vertical from compiler to rack.

**Consumer cards.** NVLink was removed from RTX 40-series onward, forcing PCIe-only multi-GPU inference on consumer cards. The **RTX 5090** (32GB GDDR7) is the current sweet spot for cost-per-token home inference. But for serious multi-GPU work, you need datacenter SKUs.

### AMD Instinct — The Challenger

- **MI300X** (Dec 2023) — 192GB HBM3, 5.3 TB/s, 5.22 PFLOPS FP8 (with sparsity). This is the chip that lets a 70B-class model fit on a single GPU without tensor parallelism. The 192GB capacity is the moat vs H100's 80GB.
- **MI300A** — APU variant with 24 Zen 4 CPU cores. Powers **El Capitan**, the Top500 #1 supercomputer.
- **MI325X** (Oct 2024) — 256GB HBM3e, 6 TB/s.
- **MI350X** (Jun 2025) — 288GB HBM3e, 8 TB/s, native FP4/FP6, CDNA4 architecture, 185 billion transistors.
- **MI355X** — 288GB HBM3e, 1,400W TDP. Highest-TDP accelerator in market. Requires liquid.

**ROCm honesty.** AMD's software stack has materially improved in 2024–2026. PyTorch is native. Major labs (Meta, Microsoft, OpenAI pilots) deploy MI300X for inference. Gaps remain: developer debugging tools, the long tail of CUDA-only kernels, and a mature NCCL-equivalent for multi-node work. ROCm is "good enough" for inference at scale, but anyone who claims parity with CUDA is selling you something.

### Intel — The Question Mark

- **Gaudi 2 / Gaudi 3** — Once Intel's bet on AI accelerators. Gaudi 3 (announced April 2024) at 128GB HBM2e and 1,835 TFLOPS FP8. Intel effectively shelved the line in 2025; the Habana business unit was put up for sale.
- **Xeon CPU with AMX** — 4th/5th Gen Xeon Scalable include Advanced Matrix Extensions (2D matrix registers + TMUL). Throughput is an order of magnitude below a GPU, but eliminates the GPU entirely. Popular for "good enough" enterprise inference on smaller models.

### Inference Accelerator Startups

These companies bet that NVIDIA's GPU architecture is not optimal for inference specifically. Each has a different architectural thesis.

- **Groq — LPU (Language Processing Unit).** Functionally-sliced SRAM-based chip with a deterministic architecture: no caches, no branch predictors, no reorder buffers. The compiler controls all execution. Generation 1 on 14nm; generation 2 (LPU v2) on Samsung 4nm. Headline: ~500 tok/s on Llama 3.3 70B and ~1,000 tok/s on gpt-oss-20b. Revenue of $500 million in 2025. Signed a roughly $20 billion licensing deal with NVIDIA in December 2025 (Groq continues independent).
- **Cerebras — Wafer-Scale Engine 3 (CS-3).** The largest AI chip ever built. 215×215mm, 4 trillion transistors, 900,000 cores, 5nm TSMC. SRAM-based (44GB on-die) with external **MemoryX** parameter server. 25kW per node. Claims 2,500 tok/s per user on Llama 4 Maverick (vs ~1,000 for Blackwell, per an unnamed independent benchmarking firm). Went public in May 2026 at a $95 billion valuation.
- **SambaNova — SN40L RDU (Reconfigurable Dataflow Unit).** Spatial dataflow architecture with three-tier memory (HBM + on-die SRAM + RDU-internal). Marketing claim: "Llama 3.1 405B on a single node" — no tensor parallelism needed. First close of $1 billion at $11 billion valuation in July 2026. Pivoting hardest of the three to sovereign AI.
- **Tenstorrent** — RISC-V + AI accelerators, led by Jim Keller. **Grayskull** (early), **Wormhole** (2024), **Blackhole** (2025). Chiplet-based. Ethernet scaling instead of NVLink/InfiniBand. ~$2 billion valuation Aug 2024.
- **d-Matrix — Corsair.** Chiplet + in-memory compute for low-precision (INT8/FP8) inference. Specifically targeted at inference, not training. Backed by Microsoft. ~$200 million total raised.
- **FuriosaAI — Renegade (ATGC).** South Korea's leading AI chip startup. TSMC 5nm, 48GB HBM3, 2 PFLOPS FP8. Reportedly rejected an $800 million acquisition offer from Meta in February 2025 to pursue an IPO.
- **Rebellions — Atom.** Korea. 24GB SRAM-only, target tokens-per-watt leader. ~30W for low-end LLM inference. Inference only, no training.
- **Sapeon — X330.** SK Telecom spinoff, Korea. Telco inference workloads. Reportedly being merged with Rebellions.
- **NeuReality — NR1 / NR1-S.** Israel. "AI-center-of-acceleration" — focuses on the CPU/network-offload part of inference (the non-matmul parts that dominate wall-clock). Claims 10× system efficiency.

### Hyperscaler Custom ASICs

The hyperscalers are not content to depend on NVIDIA. Each has built custom silicon.

- **Google TPU** — The most mature. **TPU v5e** (cost-efficient inference), **TPU v5p** (pod-scale training), **TPU v6e "Trillium"** (4,614 TFLOPS FP8, generally available October 2024), **TPU v7 "Ironwood"** (192GB HBM3e, 7.37 TB/s, announced Cloud Next April 2025). In April 2026 Google **bifurcated the TPU line for the first time**: **TPU 8t** for training (216GB, 12.6 PFLOPS FP4) and **TPU 8i** for inference (288GB, 10.1 PFLOPS FP4, with a Collectives Acceleration Engine that cuts sync latency 5×). Manufactured with Broadcom.
- **AWS Inferentia2 / Trainium2** — Trainium2 powers "Project Rainier," the Anthropic Claude cluster. AWS is uniquely secretive about Annapurna chip specs — no public FP8 TFLOPS, HBM capacity, or TDP. **Trainium4 will license NVLink Fusion** (December 2025 ServeTheHome) — a major signal that even AWS is willing to use NVIDIA's interconnect.
- **Microsoft Maia 100** — TSMC 5nm, ~105 billion transistors, rumored 64GB HBM3. First Microsoft custom AI accelerator. Powers parts of Copilot and Azure OpenAI inference. Maia 2 reportedly in development. Microsoft has never published official specs.
- **Meta MTIA v1 / v2** — Custom ASIC for Meta's ranking and recommendation workloads. MTIA v2 announced 2024. Notable signal: Meta was reportedly in talks (Nov 2025) to deploy Google TPUs in its datacenters, suggesting MTIA isn't meeting Meta's frontier-inference needs.
- **Apple Neural Engine / M-series** — The "consumer inference sleeper." The ANE in A-series and M-series SoCs delivers 16–35 TOPS INT8. The M3 Ultra's 192GB of unified memory means a desktop Mac can run Llama 3 70B in BF16 entirely in RAM. Low throughput, but a structural shift in who-pays for inference.

### Connectivity — The Hidden Layer

A modern inference workload does not run on one chip. It runs across many, and the wires between them are often the binding constraint.

- **NVLink 4.0** (Hopper) — 900 GB/s
- **NVLink 5.0** (Blackwell) — 1,800 GB/s (1.8 TB/s)
- **NVSwitch** — 64-port switch, 7,200 GB/s aggregate, with **SHARP** in-switch compute
- **NVLink-C2C** — Chip-to-chip (Grace↔Hopper), 900 GB/s
- **NVLink Fusion** (announced 2025) — Licensed to ARM, SiFive, and **AWS for Trainium4**. The first time NVIDIA has let non-NVIDIA chips use NVLink. Important erosion of NVIDIA's exclusivity.
- **PCIe 5.0** — 126 GB/s. PCIe 6.0 — 242 GB/s.
- **InfiniBand NDR/XDR** — 400 and 800 Gb/s. NVIDIA/Mellanox-dominated. The fabric of choice for HGX clusters.
- **Ultra Ethernet Consortium (UEC)** — Founded July 2023, led by AMD, Arista, Broadcom, Cisco, HPE, Intel, Meta, Microsoft. Goal: an Ethernet-based competitor to InfiniBand for AI scale-out.
- **NVIDIA Spectrum-X Ethernet** — NVIDIA's own Ethernet-for-AI play, acknowledging UEC's existence.

**Why this matters for inference.** Tensor parallelism splits each layer's matmul across GPUs. It requires all-to-all low-latency communication per layer. PCIe (126 GB/s) is roughly 7× slower than NVLink 4 (900 GB/s) and 14× slower than NVLink 5 (1,800 GB/s). On a 70B+ model, this is the difference between 30 tokens/sec and 200+.

### Cooling and Power

The faster the chip, the hotter it runs. The hotter it runs, the more it melts.

- **Air cooling** — Viable up to ~30–40 kW per rack. HGX H100 and H200 systems are typically air-cooled.
- **Direct-to-chip liquid cooling (D2C)** — Required for Blackwell (B200 at 1,000W, GB200 NVL72 at ~120 kW/rack). Cold plates on GPUs, coolant distribution units per rack.
- **Immersion cooling** — Tanks of dielectric fluid. Some CoreWeave and Meta pilots. Less mainstream than D2C.

The power question has become a strategic battleground.

- **AWS → Talen Energy** — $650 million deal for a dedicated datacenter campus at Talen's Susquehanna nuclear plant in Pennsylvania (March 2024). The first direct "nuclear plant to datacenter" co-location. FERC approved the controversial power-purchase-agreement structure in late 2024.
- **Microsoft → Constellation Energy / Three Mile Island Unit 1** — 20-year power purchase agreement (September 2024) to restart TMI-1, which closed in 2019. Branded as "Crane Clean Energy Center." Estimated restart cost ~$1.6 billion, target online date 2028.
- **Small Modular Reactors (SMRs)** — Multiple MOUs but no commercial SMR operating in the US as of mid-2026. Notable: **Helion Energy** (Sam Altman-backed fusion, $15.5 billion valuation June 2026) signed a Microsoft PPA. **Oklo**, **NuScale** (Idaho project cancelled 2023), **X-energy** (Dow Chemical deal).
- **Behind-the-meter / stranded energy** — **Crusoe Energy** repurposes flared natural gas at oil wells for AI compute. Subsidized by otherwise-wasted gas. Series D of ~$686 million in 2024 at ~$2.5 billion valuation. The catch: flaring is a regulatory target, so long-term availability is policy-dependent.

## Key Trends

**NVIDIA's grip is loosening at the edges.** NVLink Fusion licensing to AWS Trainium4 is the most important 2025 signal. AMD's MI300X has crossed the credibility threshold at major labs. Google's TPU 8i bifurcation shows the hyperscalers are willing to optimize their own silicon specifically for inference. We are nowhere near a post-NVIDIA world, but the gradient has shifted.

**Memory is the real bottleneck, not compute.** An H100 can do 4 PFLOPS of FP8 — far more than most workloads need. What limits inference is the speed at which the GPU can stream model weights and KV cache from HBM to compute. This is why HBM3e at 8 TB/s matters more than another 2× in raw FLOPS, and why Groq's and Cerebras's SRAM-based architectures are interesting at all.

**Custom silicon is no longer optional for hyperscalers.** Every hyperscaler now has at least one custom AI accelerator program. The economics of depending solely on NVIDIA at scale do not work.

**Inference accelerator startups are winning revenue, not the market.** Groq at $500 million (2025), Cerebras at $510 million, SambaNova at $11 billion valuation. Real businesses. But NVIDIA datacenter revenue is multiples of all of them combined. The startups live in the niches NVIDIA cannot or will not serve: deterministic-latency agentic inference (Groq), single-node huge-model deployment (Cerebras), sovereign AI (SambaNova).

## The Stalwart

The Stalwart of this layer is not a company. It is **HBM3e** — high-bandwidth memory stacked on top of GPUs. Three suppliers (SK Hynix, Samsung, Micron) provide the entire world's supply. Without HBM3e, NVIDIA's Blackwell is a fancy space heater. With it, the same chip serves Llama 4 at 2,500 tokens/sec. The HBM3e shortage of 2024–25 was the single biggest constraint on AI industry growth — and almost nobody outside of supply-chain analysts noticed.

If we must pick a *company* Stalwart, the answer is **FuriosaAI** — a South Korean startup that reportedly rejected $800 million from Meta to pursue an IPO. A small team in Seoul built a chip (Renegade) that competes with NVIDIA on inference at lower power. The fact that Meta wanted to buy them is the validation; the fact that they said no is the Stalwart energy.

---

*Next: [03-virtualization-cloud](03-virtualization-cloud.md)*
