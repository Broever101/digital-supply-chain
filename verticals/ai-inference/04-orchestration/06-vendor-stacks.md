# Vendor Stacks — Together, Fireworks, xAI, DeepSeek, OpenAI, Anthropic

## What Goes In

Significant engineering investment. A specific market position to defend. Usually: a proprietary inference stack.

## What Comes Out

Highly-optimized inference at scale — with the architecture kept private.

This file covers the major production inference stacks that are **proprietary or partly proprietary**. Most of them have minimal public architectural detail. Claims about their internals should be treated with calibrated skepticism.

## Together AI — The FlashAttention Team's Stack

**Source**: together.ai/blog/together-inference-engine-2 (July 2024 — dated).

**Together Inference Engine 2.0** is proprietary closed. No public statement of orchestrator (assume K8s + custom). Disclosed technical stack:

- **FlashAttention-3** integration (co-developed with Colfax/Meta/NVIDIA — up to 75% of H100 max MFU).
- **Custom GEMM + MHA kernels** "optimized for quantized inference serving by maximizing GPU's hybrid core utilization," tuned separately for prefill vs decode.
- **Proprietary quality-preserving quantization** based on **incoherence processing (from QuIP, arXiv 2307.13304)** — "spreads out outliers" per-operator. Turbo = FP8, Lite = INT4.
- **Speculative decoding**: Medusa, Sequoia, SpecExec; **custom draft models trained on RedPajama**, often beyond 10× Chinchilla optimal, tailored per customer.
- **Claimed**: >400 tok/s on Llama-3-8B; 4× faster than vLLM 0.5.1; Turbo matches FP16 quality within 8% on AlpacaEval; Lite = 12× cost reduction vs vLLM.
- **Multi-LoRA**: not specifically detailed in this blog — UNVERIFIED.
- Together has published **Sub-operators research** (StripedAttention, etc.) but no public disaggregated-P/D disclosure.

Together's positioning: vertically-integrated single-engine play. The performance edge claimed (4× vLLM) is attributed to "hybrid core utilization" tuning of kernels for quantized serving — plausible but unverifiable without source.

## Fireworks AI — "Day-0" Model Support

Proprietary; minimal public architecture detail. Known:

- "Day-0" model onboarding emphasis — first to serve new Llama/Qwen/DeepSeek releases.
- Served as a vLLM contributor early.
- The Together blog positions Fireworks as a FP8 competitor.
- Recent additions include a SQL/vector inference stack, but no engineering deep-dive source was fetchable.

**Mark most architectural claims UNVERIFIED.** Fireworks's engineering is famously private.

## xAI Colossus — The 100k H100 Cluster

Publicly disclosed:

- ~100k H100 cluster in Memphis, Tennessee.
- Expansion plans to ~1M GPUs.
- **SGLang usage** is widely cited (xAI engineers active in SGLang community) — MED confidence.

Specific orchestration (K8s? Slurm? custom?) not authoritatively disclosed. **UNVERIFIED**.

The Colossus cluster is interesting less for its architecture (which is private) and more for its scale and speed of deployment — xAI stood it up in roughly 12 months, which is unprecedented for a cluster of that size. The lesson is less "here is how to orchestrate" and more "if you have infinite capital, you can build anything quickly."

## DeepSeek — The Open-Source Innovator

**Source**: arXiv 2412.19437 (DeepSeek-V3 Technical Report, HTML v2).

DeepSeek is unique among frontier labs for the depth of their public technical disclosure. Key architectural innovations:

### MLA (Multi-head Latent Attention)

Low-rank joint key-value compression. Keys/values are down-projected to a small latent `c_t^KV` (dim `d_c ≪ d_h·n_h`), then up-projected on the fly. **Only `c_t^KV` + decoupled RoPE key `k_t^R` need to be cached** → drastically smaller KV cache than MHA at comparable quality.

This is the architectural reason DeepSeek models are cheap to serve — and why MLA is now being adopted across the ecosystem.

### DeepSeekMoE

671B total / 37B activated.

- **Auxiliary-loss-free load balancing** (pioneered in V3).
- Node-limited routing.
- No token-dropping.
- **MTP (multi-token prediction)** training objective doubles as speculative decoding at inference.

### Training Cost

- 2.788M H800 GPU-hours = **$5.576M** (at $2/GPU-hr).
- 2048-H800 cluster.
- Pretrain = 180K H800-hr per trillion tokens.
- **FP8 mixed precision** (first validation at this scale).
- **DualPipe** (computation-communication overlap, near-zero all-to-all overhead).
- Cross-node MoE without tensor parallelism.

The $5.576M training cost is the headline number that broke the narrative "frontier models cost $100M+ to train." It changed the strategic conversation about who can build frontier AI.

### Inference Deployment

- Prefill and decode use different parallelism — prefill uses **EP4×DP16-style** configs (per Dynamo blog), decode uses much wider EP (EP64) to maximize memory bandwidth.
- Production DeepSeek serving publicly documented via SGLang + Mooncake (96×H100 guide, May 2025) and Kimi-K2-style setups.
- **Hardware**: mostly NVIDIA (H800/H200/H100); some Huawei Ascend (DeepSeek-V3 paper notes Ascend support via vLLM-Ascend).

### DeepSeek V4

April 2026 release. **"Built on Chinese chips"** — Huawei and Cambricon (per Quartz and MIT Tech Review). The first publicly-disclosed frontier model trained on non-NVIDIA hardware at scale.

## OpenAI / Anthropic — The Black Boxes

Essentially no public inference-architecture detail. Both labs disclose model architecture papers (when they publish at all) but not serving infrastructure.

- **OpenAI's Triton** (the GPU kernel language, distinct from the deprecated Triton Inference Server) is public at `github.com/openai/triton` and underpins many kernels (FlashAttention historically). This is an inference-component disclosure, not an architecture disclosure.
- **Anthropic's interpretability papers** (circuits, sparse autoencoders) disclose model internals but not serving infra.
- Former-employee podcast/blog claims exist but are uniformly UNVERIFIED.

**Treat all OpenAI/Anthropic serving-stack claims as speculation.** They almost certainly run custom stacks optimized for their proprietary models. Neither has any business reason to disclose.

## What We Can Infer

Even without disclosure, some inferences are reasonable:

- **Both labs use disaggregated P/D at scale.** The economics make it non-optional.
- **Both use prefix caching aggressively.** Anthropic's pricing structure (separate cache-write fees, 5-min vs 1-hour TTLs) makes no sense otherwise.
- **Both use speculative decoding.** OpenAI's gpt-oss models and Anthropic's newer Sonnet/Haiku releases show latency profiles consistent with spec decoding.
- **Neither uses off-the-shelf vLLM or SGLang.** The performance and feature requirements are too specific.
- **Both have invested heavily in custom kernels.** OpenAI's Triton work is the public proof; Anthropic's is presumed.

## The Strategic Lesson

Closed stacks win on raw performance but lose on ecosystem contribution. Together's research papers (StripedAttention, sub-operator) and DeepSeek's full technical reports contribute more to the field than OpenAI's or Anthropic's closed serving stacks — even though the closed stacks probably run more tokens per second per dollar.

For sovereign-cloud builders, the lesson is: you can build a competitive stack on top of open-source (vLLM/SGLang/Dynamo/Mooncake/llm-d) without needing access to OpenAI's or Anthropic's secret sauce. The frontier of disclosed inference engineering is closer to the frontier of undisclosed inference engineering than the labs want you to think.

## The Stalwart

**DeepSeek** is the Stalwart of this layer — and of the entire AI inference vertical in some respects. A small team (200-ish engineers) at a Chinese quant fund published the most consequential inference-architecture paper of 2024-2025 (the V3 technical report). They gave away MLA, DeepSeekMoE, DualPipe, the FP8 training playbook, and the $5.576M training cost number. Then they trained V4 on Chinese silicon and shipped it openly. Every other frontier lab could have done what DeepSeek did. None did. That is the Stalwart move.

---

*Next: [07-cluster-schedulers](07-cluster-schedulers.md)*
