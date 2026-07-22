# The Open-Source Inference Stack on Chinese Silicon

## What Goes In

Huawei Ascend hardware (or other Chinese silicon). The same open-source inference engines (vLLM, SGLang) the world uses.

## What Comes Out

Production-grade inference on Chinese accelerators — with the same API surface developers already know, but with rough edges that represent founder opportunities.

This is the most practically important file in the Chinese-hardware chapter. The previous two files covered the hardware. This one covers the software reality: **what works, what does not, and where the gaps are that PK/GCC founders could fill.**

## The Foundational Layer — torch_npu

`github.com/Ascend/pytorch` (mirror of `gitcode.com/Ascend/pytorch`, the Chinese primary).

- 552 stars, 81 forks, **8,131 commits, 230 tags**.
- **TorchNPU 26.0.0** (April 30, 2026) supports PyTorch 2.10.0.
- Uses PyTorch's `PrivateUse1` dispatch key — the official extension mechanism for non-NVIDIA backends.
- Features: FSDP2 distributed training, DTensor, NPUGraph (graph-mode acceleration), AllGather/AllReduce/AllToAll collectives.

**This is the foundational layer**. Every other framework on Ascend sits on top of torch_npu. The fact that it exists and is actively maintained is the single most important enabler of the open-source-on-Ascend ecosystem.

The strategic choice to implement torch_npu through PyTorch's official extension mechanism — rather than forking PyTorch — is what makes the rest of this story possible. It means any investment the open-source world makes in PyTorch also benefits Ascend.

## vLLM on Ascend — Production-Ready

`github.com/vllm-project/vllm-ascend`

- **2,400 stars, 1,700 forks, 4,256 commits, 39 releases, 868 open PRs** (verified July 2026).
- **Latest stable**: v0.18.0 (April 30, 2026). Latest RC: v0.22.1rc1.
- **First stable**: v0.7.3 (May 2025). Repo created February 2025.
- **Architecture**: community-maintained hardware plugin implementing the vLLM Hardware Pluggable RFC (issue #11162). Written in C++ (51.2%) + Python (43.1%).
- **Supported hardware**: Atlas 800I A2 Inference, Atlas A2 Training, **Atlas 800I A3 Inference, Atlas A3 Training**, Atlas 300I Duo (experimental).
- **Prerequisites**: CANN 9.0.1, PyTorch 2.10.0, torch-npu 2.10.0.post2.
- **Model support**: Transformer LLMs, **MoE (DeepSeek V3/R1 family)**, Embedding models, Multi-modal LLMs — verified via support matrix docs.
- **Tencent collaboration**: Beijing meetup August 2025. Large-scale Expert Parallelism (EP) tutorial released September 2025 (v0.9.1).
- **Weekly community meeting**: SIG-Ascend, Wednesdays 15:00 UTC+8.
- **User stories**: LLaMA-Factory, verl, TRL, GPUStack — covers fine-tuning, eval, RL, deployment.

**What this means**: a founder today can deploy DeepSeek-V3 or Qwen on Ascend 910C using the same vLLM API surface they'd use on NVIDIA. **The wrapper code is portable; the kernel underneath is not.**

## SGLang on Ascend — First-Party Support

`github.com/sgl-project/sglang`

- 30,600 stars, 7,300 forks, 15,482 commits, 56 releases (v0.5.15.post1 latest, July 14, 2026).
- **README explicitly lists "Ascend NPUs" alongside NVIDIA, AMD, Intel Xeon, Google TPUs** as supported hardware.
- Powers 400,000+ GPUs in production (xAI, AMD, NVIDIA, LinkedIn, Cursor, Oracle, Google Cloud, Microsoft Azure, AWS, etc.).
- Day-0 support for DeepSeek V4 (April 2026).

**Implication**: SGLang is now the second production-grade inference engine on Ascend. This is meaningful de-risking — a single-vendor lock-in to vllm-ascend would have been a strategic liability.

## What Works

- Transformer LLM inference (Llama, Qwen, DeepSeek, etc.) — production-ready.
- MoE inference (DeepSeek V3/R1) — production-ready with EP.
- Embedding models — production-ready.
- Multi-modal models (LLaVA-class) — production-ready.
- Fine-tuning via LLaMA-Factory, verl, TRL — works.
- Continuous batching, PagedAttention (Ascend implementation) — works.
- Speculative decoding — UNVERIFIED status on Ascend.
- Long context (up to 256k for supported models) — works.

## What Does Not Work (or works less well)

- **The CANN stack is closed-source.** Unlike CUDA, you cannot easily inspect or modify the underlying kernels. This slows community contribution.
- **FlashAttention on Ascend exists but trails the CUDA version by 6–12 months.** Performance gaps in the latest techniques.
- **Quantization kernels (AWQ, GPTQ, FP4) on Ascend are sparse.** Most cutting-edge quantization research happens on CUDA first.
- **First-class OpenTelemetry integration for torch_npu does not exist.** Observability is a real gap.
- **llama.cpp on Ascend** — status UNVERIFIED. The llama.cpp project supports CUDA, Metal, ROCm, SYCL, Vulkan backends; Ascend support status unclear.
- **Huawei Ascend documentation portal (hiascend.com) is gated behind a login.** This is a structural barrier to outside contribution.

## The Performance Reality

| Workload | NVIDIA H100 | Ascend 910C | Notes |
|---|---|---|---|
| Inference throughput (DeepSeek R1) | 100% (baseline) | **~60%** | DeepSeek researchers' own number |
| Perf-per-watt (cluster) | NVL72 baseline | **2.3× worse** (CloudMatrix 384) | Verified |
| Training (V4) | Standard | Viable | DeepSeek V4 trained on Chinese chips (Apr 2026) |
| Software parity | CUDA ecosystem | vLLM + SGLang both first-party | As of mid-2026 |
| Kernel ecosystem maturity | Comprehensive | Sparse, lagging 6-12 months | Real gap |
| Documentation | Comprehensive, open | Gated, closed | Real gap |
| Community size | Massive global | Mostly Chinese | Real gap |

The pattern: API parity is good. Performance parity is partial. Kernel ecosystem is the bottleneck.

## Gaps and Founder Opportunities

This is the most actionable section. The structural gaps in the open-source-on-Chinese-silicon stack are real and unaddressed.

### Software Gaps (High Leverage, Low Capital)

1. **Cross-vendor inference abstraction** — There is no "LiteLLM but for hardware." vLLM has hardware plugins (vllm-ascend, vllm-rocm, etc.) but no clean abstraction for routing across them. A founder could build a thin abstraction layer that lets a single API surface dispatch to NVIDIA, Ascend, or hybrid fleets. **The vLLM Hardware Pluggable RFC (#11162) is the seam.**

2. **Observability for heterogeneous fleets** — Datadog, Grafana, and Prometheus all assume NVIDIA. Ascend metrics (CANN NPU profiling, HCCS bandwidth, NPUGraph stats) require custom exporters. **No first-class OpenTelemetry integration exists for torch_npu.** This is a 6-month build for a 2-person team.

3. **Billing and FinOps for mixed-vendor GPU clusters** — CloudHealth, Vantage, etc. are NVIDIA-centric. GCC sovereign AI clouds running mixed NVIDIA+Ascend fleets have no off-the-shelf cost-allocation tooling.

4. **Model-format conversion** — Most open weights are safetensors with CUDA-optimized layouts. Ascend prefers a different memory layout for optimal HBM bandwidth. There is no one-click converter; every team rolls their own.

5. **Kernel ecosystem** — FlashAttention on Ascend exists but trails the CUDA version by 6–12 months. Quantization kernels (AWQ, GPTQ, FP4) on Ascend are sparse. **A Pakistani engineering team that ports and maintains state-of-the-art kernels to Ascend would have a defensible open-source moat.**

### Localization Gaps (High Leverage, Market-Specific)

1. **Arabic-optimized inference** — Qwen, DeepSeek, and ERNIE all support Arabic, but tokenization and KV-cache behavior for Arabic-script languages is suboptimal. A GCC founder could build Arabic-specific vLLM/SGLang configurations, prefix-cache strategies, and tokenizer optimizations.

2. **Urdu language models on Ascend** — Almost nothing exists. This is greenfield.

3. **Shariah-aware model serving** — Filtering, red-teaming, and content moderation for GCC regulatory environments, deployed as a vLLM/SGLang middleware.

### Integration Gaps (Medium Leverage, Harder Build)

1. **GCC-region Ascend distribution** — No authorized Ascend reseller exists in the GCC today (to public knowledge — UNVERIFIED). A Pakistani founder with a Dubai entity could become the regional integrator for Atlas servers, replicating the model of NVIDIA's regional distributors.

2. **Hybrid NVIDIA/Ascend reference architectures** — For GCC sovereign clouds that must run both (to hedge US-China policy risk), there is no documented reference architecture. A consulting/systems-integration play.

### Cross-Platform Abstractions (High Leverage, Strategic)

1. **A "Hardware-agnostic inference gateway"** — Think Kong for LLMs, but with hardware-aware routing: send latency-sensitive requests to NVIDIA, batch throughput to Ascend, fallback to CPU. SGLang's router is NVIDIA-only today.

2. **A unified benchmark suite for Chinese silicon** — MLPerf is NVIDIA-dominated. There is no independent benchmark body publishing comparable numbers across Ascend, Cambricon MLU, Biren BR100, MetaX C500. **Founders who build this become the reference.**

## What Not to Build

- **Another Chinese GPGPU.** There are already 8+ funded players; market is consolidating.
- **Another LLM training framework.** PyTorch + torch_npu + MindSpore is enough.
- **A CUDA clone.** Ten years too late; the war is over operating *above* CUDA via vLLM/SGLang.

## The Stalwart

The Stalwart of this layer is the **vllm-ascend community** — a global group of contributors (Tencent, Huawei, Moonshot, ByteDance, indie developers) who have built and maintained a production-grade inference engine plugin for non-NVIDIA hardware. 2,400 stars, 1,700 forks, weekly SIG meetings. They have done the unglamorous engineering that makes the rest of the open-source-on-Ascend ecosystem possible.

The hidden Stalwart: the **vLLM Hardware Pluggable RFC (#11162)** — the architectural decision that made vllm-ascend possible. By defining a clean hardware-plugin interface, vLLM ensured that the next decade of accelerator diversity (Ascend, TPU, Trainium, MI300, custom ASICs) could plug into the same inference API. This is one of the most consequential architectural decisions in the inference ecosystem, and almost nobody outside the vLLM community knows it happened.

---

*End of Chinese hardware chapter. Return to [vertical README](../00-executive-summary.md).*
