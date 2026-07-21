# Other Chinese AI Hardware Vendors

## What Goes In

The same SMIC/TSMC fab capacity. Chinese government subsidies. A wave of VC funding and national-champion industrial policy.

## What Comes Out

A roster of Chinese AI accelerator companies, most of which IPO'd in late 2025 / early 2026, none of which has the same stack + deployment + volume combination as Huawei Ascend.

Huawei is the only Chinese AI silicon vendor with credible production maturity at scale. The other vendors are real companies with real products, but their strategic positions are weaker. This file maps them honestly.

## Cambricon (中科寒武纪) — The Pure-Play Specialist

- **Ticker**: SSE STAR 688256 (NOT delisted — common misconception).
- **2024 revenue**: CN¥1.18B. First quarterly profit late 2024 after 7 years of losses.
- **February 2026**: announced maiden dividend after profitable 2025.
- **2024 stock**: +383% — China's top-performing stock of the year.
- **980 employees** (2024).
- **Entity List**: December 15, 2022.
- **Notable history**: Originally supplied NPUs to Huawei for Kirin 970 (Mate 10, 2017). **Huawei terminated the contract in 2018** to in-house to HiSilicon, which nearly killed Cambricon.
- **Products**: MLU370 (inference), MLU590 (newer). **NeuWare** software stack, **MagicMind** inference framework. Specs UNVERIFIED in this research pass; MED confidence from prior knowledge.
- **Strategic position**: Pure-play Chinese AI chip designer, government-affiliated, profitable, but no longer a Huawei supplier. The fact that Cambricon survived Huawei's 2018 in-housing is itself notable.

## Biren Technology (壁仞) — The Sanctions Poster Child

- **Ticker**: SEHK 6082 (IPO **January 2, 2026** on HKEX).
- **Founded 2019** by Lingjie Xu (ex-NVIDIA) and others.
- **Products**: BR100 (77 billion transistors, claimed 2 PFLOPS AI perf, TSMC 7nm — Tom's Hardware verified), BR104 (smaller variant).
- **The sanctions story**: TSMC halted all BR100 fabbing after October 2022 controls (Bloomberg verified). Biren reportedly **modified BR100 to process less data** to evade thresholds (FT verified). Added to Entity List October 2023.
- **Funding**: $280M from Guangzhou state investors (December 2023); 1.5B yuan total by June 2025; IPO completed January 2026.
- **Customers**: China Mobile, China Telecom intelligent computing centers (SCMP verified).
- **Software stack "BIRENSUPA"** — UNVERIFIED, LOW confidence.
- **Status**: Shipping in limited production at Chinese hyperscalers. Co-founder Xu Lingjie left post-sanctions.

Biren is the canonical example of how US export controls reshape the Chinese AI silicon industry. Pre-sanctions it was a serious NVIDIA competitor at the high end. Post-sanctions it is surviving on state subsidies and downgraded chips.

## Moore Threads (摩尔线程) — The Dark Horse

- **Ticker**: SSE STAR 688795 (IPO **December 5, 2025**; raised >$1B; +468% on debut).
- **Founded October 2020** by Zhang Jianzhong (former NVIDIA China VP).
- **Top institutional investor**: **Liang Wenfeng** — the DeepSeek/High-Flyer founder (verified December 2025 SCMP). This is a *major* signal.
- **Backers**: ByteDance, Tencent, Sequoia China, GGV, Shenzhen Capital.
- **Products**: MTT S60/S80 (consumer/desktop), MTT S2000 (server), MTT S4000 (datacenter). S4000 specs UNVERIFIED.
- **MUSA** (Moore Threads Unified System Architecture) software stack — CUDA-like.
- **Process**: 12nm (older than competitors).
- **Entity List**: October 2023.
- **API support**: DirectX, Vulkan, OpenGL (consumer GPU roots; AI is secondary).
- **Status**: Public market darling but still primarily a graphics/AI hybrid play. Not a serious training-accelerator alternative yet.

The DeepSeek founder's investment is the strategically interesting fact. If Moore Threads becomes the consumer-AI inference play (gaming GPUs that also do LLM inference), the DeepSeek connection could matter a lot.

## Iluvatar CoreX / Tianshu Zhixin (天数智芯)

- **Ticker**: SEHK 9903 (IPO **January 8, 2026**).
- **Founded December 2015** by Li Yunpeng (ex-Oracle).
- **Backers**: Centurium Capital, HongShan (ex-Sequoia China).
- **Products**:
  - **Big Island / TianGai-100** (January 2021) — 7nm training GPGPU, claimed A100/MI100-class (PC Gamer verified).
  - **Zhikai-100** (May 2022) — 7nm inference GPGPU.
- **Status**: First "domestic 7nm GPGPU." Received 230M yuan orders by September 2021.
- **Chairman drama**: Diao Shijing (ex-Tsinghua Unigroup co-president) appointed May 2021, **detained in anti-graft probe July 2022**.
- **Software stack "Starspring"** — UNVERIFIED, LOW confidence.

## Hygon (海光) — The AMD JV Derivative

- **Ticker**: SSE 688041 (SSE 50 constituent — major cap).
- **Founded 2014** as Tianjin Haiguang. IPO August 2022 raised 10.6B yuan.
- **Origin**: 2016 AMD JV — licensed **Zen 1 x86 IP for $293M** via two JVs (HMC and HICD).
- **First product**: Hygon Dhyana SoC (2018), China-market Zen 1 derivative.
- **Entity List**: June 2019 — AMD cannot license additional IP, but Hygon retains rights to existing Zen 1 IP.
- **2023 revenue**: CN¥6.01B. 1,790 employees.
- **DCU (Deep Computing Unit)** — GPU line, AMD CDNA-derived. Specifics UNVERIFIED; ~MI100 class.
- **Merger with Sugon**: announced May 2025, **called off December 10, 2025** (Nikkei verified).
- **Status**: Most credible Chinese x86 path. GPUs are derivative of older AMD IP, not cutting-edge.

Hygon matters because it is the only Chinese company that can legally produce x86 CPUs — critical for general-purpose datacenter workloads that need to run alongside AI inference. The DCU line is less interesting; older AMD IP cannot compete with H100.

## MetaX (沐曦) — The AMD Alumni Play

- **Ticker**: SSE 688802 (IPO **December 17, 2025**, +693% debut — Bloomberg).
- **Founded September 2020** by ex-AMD engineers (Chen Weiliang et al.).
- **Backers**: China Structural Reform Fund, China Internet Investment Fund, Lightspeed, Sequoia China, Matrix Partners China.
- **Products**:
  - **C500** (June 2023) — 7nm training GPU, ~75% of A100 perf, 15 TFLOPS FP32. **CUDA-compatible** (per Wikipedia).
  - **N100** — inference GPU, HBM2E, 160 TOPS INT8 / 80 TFLOPS FP16.
  - **C280** — downgraded design submitted to TSMC late 2023 to comply with US restrictions (Reuters verified).
- **Traction**: 9 compute clusters across China, 10,000+ GPUs in commercial operation by end of 2024.
- **Software stack "MACA" / "MAC-X"** (MACA 2.0 launched June 2023 with C500).
- **Status**: Real traction at Chinese AI compute centers. **CUDA-compatibility is its main differentiator**.

MetaX is the most interesting non-Huawei Chinese AI chip company because of CUDA compatibility. If you can take existing CUDA code and run it on MetaX with minimal changes, you have a real NVIDIA alternative for the China market.

## Enflame (燧原) — The Tencent-Backed Play

- **Status**: Private (IPO planned on SSE STAR, CICC advising, announced August 2024).
- **Founded March 2018** by ex-AMD (Zhao Lidong, Zhang Yalin).
- **Tencent-backed** (key strategic investor); also CICF (national chip fund).
- Behavior: Like MetaX, submitted downgraded designs to TSMC in late 2023 (Reuters June 2024).
- **Wuxi AI computing hub** partnership (October 2024).
- **Status**: Selling primarily to SOEs and government compute centers. Not a serious commercial play.

## The HBM Bottleneck — CXMT and SMIC

The real story of Chinese AI silicon is not the chips — it is the supply chain that makes them possible.

### CXMT (ChangXin Memory Technologies) — China's Only DRAM/HBM Hope

- Founded May 2016. HQ Hefei, Anhui. ~3,000 employees (2019).
- Currently: **4% of global DRAM market** (Q2 2025), aiming for 10% by Q4 2025.
- 720,000 wafers/quarter by end of 2025.
- **DDR5 in production** since early 2025.
- **HBM**: Reuters reported CXMT aims to begin HBM production by **end of 2026** at a Shanghai back-end packaging facility. This is the critical bottleneck for Chinese AI chips.
- **IPO**: planned SSE STAR raise of 29.5B yuan ($4.2B), CSRC approved June 2026, **212× oversubscribed retail July 16, 2026** — the largest Chinese IPO of 2026.
- Q1 2026 revenue: 50.8B yuan (+700% YoY).
- US federal procurement ban 2022 NDAA. **US DoD added CXMT to Chinese military companies list June 2026**.
- IP theft cloud: 10 former Samsung employees arrested 2023; 2 SK Hynix engineers convicted.

### SMIC (Semiconductor Manufacturing International Corp) — The Fab

- Makes Ascend 910B (N+1 process) and 910C (N+2 process).
- N+2 is SMIC's 2nd-gen 7nm-equivalent, achieved without EUV (uses DUV multi-patterning).
- This is the manufacturing limit of Chinese-domestic fab capability today.

### TSMC Dependency Matrix

| Vendor | TSMC-fabbed | SMIC-fabbed |
|---|---|---|
| Huawei Ascend 910B/C | Pre-sanctions only | Current |
| Biren BR100 | Pre-sanctions | Post-sanction (downgraded) |
| MetaX C500 | Pre-sanctions | C280 downgrade post-sanctions |
| Iluvatar Big Island | Yes | — |
| Enflame | Yes (downgraded) | — |
| Moore Threads S80 | Yes | — |

**The Chinese AI silicon industry is structurally capped at SMIC N+2 (7nm-class)** until CXMT HBM and domestic EUV alternatives mature.

## What PK/GCC Founders Should Care About

For a Pakistani or GCC founder, the relevant question is not which Chinese chip is technically best. It is which chip has the most credible software stack, the most reliable supply chain, and the lowest integration risk. The answer in 2026 is **Huawei Ascend, by a wide margin**.

The other vendors matter for three reasons:

1. **They exist.** A multi-vendor Chinese silicon ecosystem is healthier than a Huawei monopoly. It gives the China stack resilience.
2. **They will be acquired or absorbed.** Expect consolidation. The 8+ funded AI chip startups will not all survive.
3. **Some have niches.** MetaX's CUDA compatibility is real. Cambricon's government affiliation matters for Chinese sovereign deals. Hygon's x86 license matters for general-purpose compute.

For practical purposes: bet on Ascend. Watch Cambricon, MetaX, and Biren as the second tier. Ignore the others for now.

## The Stalwart

The Stalwart of this layer is **MetaX's CUDA compatibility**. A small team of ex-AMD engineers built a 7nm GPU that runs existing CUDA code with minimal modification — the only credible "drop-in NVIDIA alternative" from a Chinese vendor. Whether MetaX survives as an independent company or gets absorbed into a larger player, the CUDA-compatibility approach is the right engineering bet.

The other Stalwart: **CXMT**, China's only DRAM/HBM hope. The fact that CXMT plans to begin HBM production by end of 2026 is the most important supply-chain event in Chinese AI silicon. Without domestic HBM, the entire Chinese AI chip industry is capped.

---

*Next: [03-software-stack](03-software-stack.md)*
