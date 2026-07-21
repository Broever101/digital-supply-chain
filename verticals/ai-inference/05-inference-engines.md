# Layer 4: Inference Engines

## What Goes In

Model weights (a `.safetensors` or `.gguf` file). A request stream — prompts coming in from an API. A cluster of GPUs to run them on.

## What Comes Out

Tokens, streamed back to the user as fast as the silicon allows.

This is the layer where the actual computation happens. Everything below (hardware, cloud, orchestration) exists to make this layer work. Everything above (gateways, billing, observability) exists to wrap this layer in commercial plumbing.

The inference engine is the single most consequential piece of software in the AI inference stack. The choice of engine determines throughput, latency, supported features, and hardware utilization. It is also the most competitively contested layer — multiple open-source projects, each backed by serious institutions, racing to be the default.

## The Engines — A Deep Comparison

| Engine | License | Maintainer | GitHub Stars | Hardware | Defining Feature |
|---|---|---|---|---|---|
| **vLLM** | Apache-2.0 | Community + NVIDIA / Neural Magic (UC Berkeley origin) | **86.7k** | NVIDIA, AMD, Intel Gaudi, TPU, CPU, AWS Neuron, Huawei Ascend, Apple Silicon | PagedAttention; ecosystem default |
| **SGLang** | Apache-2.0 | LMSYS (Lianmin Zhu) | **30.5k** | NVIDIA, AMD MI300/MI355, Intel CPU, TPU, Ascend | RadixAttention, structured generation, expert parallelism at scale |
| **llama.cpp** | MIT | ggml-org (Georgi Gerganov + ~30 maintainers) | **121k** | CPU, CUDA, Metal, ROCm, Vulkan, SYCL, CANN, WebGPU | GGUF format, k-quants, runs anywhere |
| **Ollama** | MIT | ollama Inc. | **176k** | Wraps llama.cpp + MLX | DX layer, OpenAI-compatible |
| **TensorRT-LLM** | Apache-2.0 | NVIDIA | **14.2k** | NVIDIA only | Best raw H100/B200 throughput, FP8, kernel fusion |
| **HF TGI** | Apache-2.0 | Hugging Face | **10.9k — ARCHIVED March 2026** | NVIDIA, AMD, Inferentia, Intel Gaudi, TPU | Rust router + gRPC; **deprecated** |
| **MLX** | MIT | Apple (ml-explore) | **27.6k** | Apple Silicon (also CUDA + CPU Linux as of 2026) | Unified-memory array framework |
| **ExLlamaV2 / V3** | MIT | Turboderp | **4.6k (V2 archived → V3)** | NVIDIA consumer only | EXL2 mixed-bit quantization (2–8 bpw) |
| **DeepSpeed-MII / FastGen** | Apache-2.0 | deepspeedai (Microsoft origin) | **2.1k** | NVIDIA | Dynamic SplitFuse; **largely stalled** |
| **LMDeploy** | Apache-2.0 | InternLM (Shanghai AI Lab) | **8k** | NVIDIA, Ascend, Cambricon | TurboMind (C++) + PyTorch; strong on Chinese models |
| **Dynamo** | Apache-2.0 | ai-dynamo (NVIDIA) | **7.5k** | Multi-engine orchestrator | Disaggregated P/D, KV-aware routing (see Layer 3) |
| **Mooncake** | Apache-2.0 | kvcache-ai (Moonshot AI / Tsinghua) | **5.9k** | Multi-vendor | KVCache-centric disaggregated architecture |

Star counts verified July 20, 2026. They drift fast — vLLM gains roughly 1,000 stars per week.

### vLLM — The Default

If you serve a model in production today, you probably use vLLM. Born from a SOSP 2023 paper on **PagedAttention** out of UC Berkeley's Sky Computing Lab, vLLM has become the open-source default for serving LLMs on GPUs.

The defining innovation is **PagedAttention** — operating-system-style virtual paging of the KV cache into fixed blocks. Before PagedAttention, KV cache memory was allocated in contiguous chunks per request, leading to 60–80% fragmentation. After PagedAttention, fragmentation dropped below 4%, and throughput improved 2–4× at equal latency. Every modern engine has since implemented it.

Modern vLLM stacks: PagedAttention + continuous batching + prefix caching (default on) + speculative decoding (Medusa, EAGLE) + chunked prefill + FP8/MXFP4/NVFP4 quantization + multi-LoRA + distributed KV cache via NIXL. Supports NVIDIA, AMD, Intel Gaudi, TPU, AWS Neuron, Huawei Ascend, and Apple Silicon. The community is now led by a mix of academics and engineers from NVIDIA and Neural Magic (which NVIDIA acquired).

### SGLang — The Challenger With A Different Thesis

SGLang was born at LMSYS (the Chatbot Arena people) out of research by Lianmin Zhu at UC Berkeley. Its defining innovation is **RadixAttention** — organizing the KV cache as a radix tree keyed by token prefixes.

The difference matters. vLLM's prefix cache hashes each KV block by its tokens and parent hash, then reuses blocks on exact prefix matches. SGLang's radix tree handles partial overlap and eviction better, and supports more sophisticated cross-request reuse patterns. The result: up to **6.4× throughput** on agentic, structured, and few-shot workloads where prefixes repeat.

SGLang also leads on **structured generation** — constrained decoding for JSON, regex, and grammar-based outputs. Its compressed-FSM approach is the fastest in the ecosystem. If you are building agents that need reliable tool calls, SGLang is the engine of choice.

SGLang production adopters: **xAI** (uses it for Grok 2.5), **AMD, NVIDIA, Intel, LinkedIn, Cursor, Oracle Cloud, Google Cloud, Microsoft Azure, AWS, Atlas Cloud, Voltage Park, Nebius, DataCrunch, Novita, Modal, Baseten**. The README claims "400k+ GPUs worldwide" running SGLang. The repository has moved from `sglang-project/sglang` to `sgl-project/sglang` — a sign of org-level maturation.

### llama.cpp — The Stalwart

Built solo by **Georgi Gerganov** — originally a Bulgarian researcher who wanted to run LLaMA on his MacBook. Now a project with ~30 maintainers and 121,000 GitHub stars. The most widely deployed inference engine in the world, by install count, because it is what runs underneath Ollama, LM Studio, gpt4all, llamafile, LocalAI, and TabbyAPI.

The defining innovations: the **GGUF** format (a single file containing weights + tokenizer + metadata) and **k-quants** (Q4_K_M, Q5_K_M, etc. — community-named sweet spots for quality-per-bit). llama.cpp does not need a GPU. It does not need Linux. It does not need a server. It runs on a CPU, on Apple Metal, on Vulkan, on a phone. It is the gateway drug to local LLMs.

The Stalwart energy is unmistakable. A solo developer (initially) built the most-used inference engine in the world. Then he open-sourced it, gave it away, and quietly kept improving it.

### Ollama — The DX Layer

Ollama is to llama.cpp what Docker is to Linux containers. The same underlying tech, wrapped in a developer experience so clean that `ollama run llama3.2` is the standard first command a new AI developer learns.

The business model is interesting: the local runtime is free and open source (176,000 stars), and the cloud upsell is $20/month for Pro (3 cloud models in parallel, 50× usage) or $100/month for Max (10 cloud models, 5× Pro usage). The local product is the moat; the cloud is the revenue.

### LM Studio — The GUI Counterpart

Closed-source desktop app from Element Labs. The most polished GUI for downloading, managing, and chatting with local models. Also built on top of llama.cpp. Recently launched **LM Studio Bionic** — a pivot from "model runner" to "agent runtime." Whether this pivot works is an open question.

### TensorRT-LLM — NVIDIA's Performance Play

If vLLM is the default and SGLang is the challenger, TensorRT-LLM is the "I have NVIDIA hardware and I need every last token-per-second out of it" option. Built by NVIDIA, optimized specifically for H100, H200, B100, B200.

The defining features: kernel fusion, in-flight batching, FP8 and FP4 inference, deep integration with NVIDIA hardware. Now Apache-2.0 (fully open-sourced in March 2025 with development moved to GitHub; before that it was open Python APIs over a closed C++ runtime). Public adopters: Databricks, Snowflake, NAVER Place, Microsoft Bing.

The trade-off: TensorRT-LLM is NVIDIA-only, slower to add new model architectures, and the development velocity is lower than vLLM or SGLang. The open question for 2026 is whether vLLM and SGLang will catch up to TensorRT-LLM's raw throughput on NVIDIA hardware, or whether NVIDIA's compiler advantage remains durable.

### HF TGI — Deprecated

Hugging Face's Text Generation Inference. Apache-2.0 since April 2024. **Archived March 21, 2026.** Hugging Face now recommends vLLM, SGLang, llama.cpp, or MLX instead. The lesson: even well-funded commercial open source does not survive if it does not win the technical race.

### MLX — Apple's Quiet Winner

Apple's array framework, optimized for Apple Silicon's unified memory. The most-used inference engine on Mac, by far. Recently added CUDA and CPU Linux support, expanding its reach. Now 27.6k stars.

The strategic significance is larger than the star count suggests. Every Mac with an M-series chip is a viable local-inference machine because of MLX. Apple's unified memory architecture means an M3 Ultra desktop can run Llama 3 70B in BF16 entirely in RAM — no GPU required. This is a structural shift in who-pays for inference.

### ExLlamaV3 — The Consumer-GPU Specialist

Built by **Turboderp** (solo developer, like llama.cpp). The fastest engine for running quantized models on consumer NVIDIA GPUs (RTX 3090, 4090). Uses the EXL2 format — mixed-bit quantization where each layer is allocated bits based on sensitivity, typically averaging 4.5–5 bits per weight.

ExLlamaV2 was archived in mid-2025; V3 development is ongoing. Niche but beloved in the r/LocalLLaMA community.

### LMDeploy — The Chinese Challenger

From the InternLM team at Shanghai AI Lab. TurboMind (C++) and PyTorch backends. Strong support for Chinese models (InternVL, Qwen) and Chinese hardware (Huawei Ascend, Cambricon). 8k stars. Underappreciated outside China, but a real presence within it.

### Dynamo and Mooncake — The Orchestrators (Covered In Layer 3)

Listed here for completeness. Dynamo (NVIDIA) and Mooncake (Moonshot AI / Tsinghua) sit between orchestrators and engines — they coordinate other engines across nodes, with KV cache as a first-class object. Mooncake's Transfer Engine delivers 87–190 GB/s on 4×200 or 8×400 Gbps RoCE — 2.4× to 4.6× TCP. It is the reference architecture for the disaggregated prefill/decode pattern that defines 2026 SOTA.

## Key Innovations Explained

These are the ideas that have shaped inference engineering since 2023. Each one is a paper worth reading.

### PagedAttention (vLLM, SOSP 2023)

The foundational inference-systems innovation of the era. OS-style virtual memory for the KV cache: blocks are non-contiguous, mapped via block tables. Cuts KV memory waste from 60–80% to under 4%; delivers up to 24× throughput vs naive, 2–4× vs FasterTransformer. Now universal baseline — every modern engine implements it.

Paper: Kwon et al., "Efficient Memory Management for LLM Serving with PagedAttention," SOSP 2023. arXiv: 2309.06180.

### Continuous Batching (Orca, OSDI 2022)

The single biggest throughput unlock of the era. Instead of waiting for a batch to complete, admit and evict requests every iteration. Combined with PagedAttention, this is the foundational vLLM design. The Anyscale benchmark showed up to **23× improvement** over naive static batching, **8× improvement** over HF TGI's continuous batching, and **4× improvement** over NVIDIA FasterTransformer's static batching.

Counter-intuitively, continuous batching improves P50/P90 latency too, not just throughput — it lets the scheduler inject new low-latency requests while big batches run.

### RadixAttention (SGLang, 2023)

Organize the KV cache as a radix tree keyed by token prefixes. Enables automatic, fine-grained cross-request reuse — major wins (3–6×) for agentic, RAG, few-shot, and structured-output workloads where prefixes repeat. Distinct from vLLM's hash-based prefix cache, which handles exact matches but is less flexible on partial overlap.

Paper: Zheng et al., "SGLang: Efficient Execution of LLM Programs." arXiv: 2312.07104.

### Chunked Prefill (Sarathi-Serve, OSDI 2024)

Split long prefill into near-equal chunks; batch one prefill chunk alongside many decodes per iteration. Eliminates the "prefill stalls decode" stall — flattens the throughput-latency curve. Now in vLLM, SGLang, and TensorRT-LLM.

Paper: Agrawal et al., "Taming Throughput-Latency Tradeoff with Sarathi-Serve," OSDI 2024. arXiv: 2403.02310.

### Speculative Decoding (2023→2026)

A small "draft" model (or extra heads on the main model) proposes multiple tokens; the main model verifies them in one forward pass. Lossless in quality. The speedup comes from amortizing the memory-bandwidth cost of the big model's forward pass across multiple tokens.

- **Vanilla speculative decoding** (Leviathan 2023, Chen 2023) — 2–3× speedup. Requires a separate aligned draft model.
- **Medusa** (Cai et al., ICML 2024) — Adds extra heads on the same model. No draft model needed. Medusa-1: 2.2× lossless. Medusa-2: 2.3–3.6×.
- **EAGLE / EAGLE-2 / EAGLE-3** (Li et al., NeurIPS 2025) — Current state of the art. Feature-level autoregressive speculation. **4.1–6.5× speedup** at T=0 on Llama 3.1 8B and 3.3 70B. Distribution-preserving.

The trade-off: speculative decoding is net positive when you are latency-bound (interactive, agentic) and net negative when your GPUs are already saturated (you burn extra verifier FLOPs to save wall-clock).

### Disaggregated Prefill/Decode (Mooncake, FAST 2025 Best Paper)

Run prefill and decode on separate GPU pools, with KV cache transferred between them over RDMA. Prefill is compute-bound; decode is memory-bandwidth-bound. Doing both on the same GPU forces compromises. Doing them separately lets neither be compromised.

Mooncake's Transfer Engine delivers 87–190 GB/s on 4×200 / 8×400 Gbps RoCE — 2.4× to 4.6× the throughput of TCP. This is the 2026 SOTA at hyperscale. DeepSeek-V3's architecture was designed around this pattern. Now in vLLM, SGLang, TensorRT-LLM, Dynamo, and LMDeploy.

Paper: Qin et al., "Mooncake: A KVCache-centric Disaggregated Architecture," FAST 2025 Best Paper. arXiv: 2407.00079.

### FP8 Inference

Native on H100 / H200 / B200. NVIDIA's paper (Micikevicius et al., 2022) showed FP8 (E4M3 and E5M2 formats) matches FP16 quality on transformers up to 175 billion parameters, at roughly 2× the throughput. The default for any production deployment on Hopper-or-later hardware.

Paper: arXiv: 2209.05433.

### Prefix Caching

Hash each KV block by its tokens + parent hash; reuse on shared prefixes (system prompts, few-shot examples, multi-turn chat history). Almost a free lunch for agentic and RAG workloads where prefixes repeat. vLLM defaults it on.

A non-obvious concern: prefix caching introduces a **timing side-channel**. An attacker can probe whether a prefix was seen by measuring latency. vLLM's `cache_salt` parameter is the mitigation — it injects a per-request salt into the block hash. Multi-tenant deployments should treat this as a real threat model.

### Constrained / Structured Decoding

Filter logits so output conforms to a JSON schema, regex, or context-free grammar. Essential for reliable tool calls and structured outputs.

- **xgrammar** (MLC) — Typically 3.5× faster than outlines on JSON, 10× on CFG. The current speed leader.
- **outlines** — The older alternative. Slower but more mature ecosystem.
- **lm-format-enforcer** — Another option, popular in the HF ecosystem.

vLLM and SGLang both ship pluggable backends, so you can swap constrained-decoding implementations without changing your model serving code.

### Quantization

Covered in detail in Layer 0 (End-User Lens). The short version: FP8 on Hopper-and-later is near-free (2× throughput, no quality loss). 4-bit methods (AWQ, GPTQ) are near-lossless for instruction-tuned models and give 3×+ speedups. Below 4 bits, quality degrades non-linearly.

### Tensor vs Pipeline vs Expert Parallelism

How to split a model across multiple GPUs.

- **Tensor parallelism** — Shard each layer's matmul across GPUs. Requires all-to-all communication per layer. NVLink-bound.
- **Pipeline parallelism** — Split layers across GPUs. Bubble-bound (GPUs sit idle waiting for the previous stage).
- **Expert parallelism** — For Mixture-of-Experts models. Shard experts across GPUs. The DeepSeek / Kimi / Mixtral pattern. DeepEP is the open implementation.

Modern DeepSeek-class deployments combine all three plus prefill/decode disaggregation. There is no single right answer; it depends on the model, the cluster topology, and the workload.

## Key Trends

**The default has shifted from "build your own" to "vLLM or SGLang."** Three years ago, every inference provider rolled their own engine. Today, even Together AI — historically the most proprietary — uses a custom engine heavily based on FlashAttention but is the exception, not the rule. Most providers use vLLM or SGLang as the base and add their own special sauce.

**Disaggregated prefill/decode is the new architecture floor.** If you are not running PD-disaggregation at hyperscale, you are leaving 2–4× throughput on the table. Mooncake's KV-cache transfer layer is becoming the universal abstraction.

**Structured generation is becoming a competitive battleground.** As agents proliferate, the ability to reliably produce valid JSON / tool calls / structured outputs becomes critical. SGLang's compressed FSM and xgrammar are the leaders. vLLM ships pluggable backends.

**Hugging Face TGI's deprecation is a warning.** A well-funded commercial open-source project, archived in March 2026 because it lost the technical race. The lesson: in inference, the better-engineered solution wins, regardless of who backs it.

**Apple's MLX is the silent winner of the local-inference tier.** Every Mac is a viable local-inference machine because of MLX. Combined with llama.cpp and Ollama, Apple Silicon is a structural presence in the inference market that almost no one in the datacenter-centric discourse talks about.

## The Stalwart

**llama.cpp** is the unambiguous Stalwart of this layer — and arguably of the entire AI inference vertical. A solo developer, Georgi Gerganov, built the most-deployed inference engine in the world. He gave it away. He kept improving it. It runs on your phone. It runs on a Raspberry Pi. It runs on a 16-year-old's gaming PC. The GGUF format is the de-facto standard for distributing model weights. Every local-LLM product — Ollama, LM Studio, gpt4all, llamafile, LocalAI — is a wrapper around it.

Other Stalwarts in this layer:

- **Mooncake Transfer Engine** — quietly becoming the universal KV-cache transport layer. Integrated into six-plus major engines. Small, focused, doing one job exceptionally well.
- **xgrammar** — typically 3–10× faster than the alternatives for structured decoding. Underused outside SGLang and vLLM.
- **llamafile** (Mozilla Builders) — Single-file distributable LLM using Cosmopolitan libc. Cross-platform (Windows, macOS, Linux, BSD). Underappreciated as a deployment primitive. The cleanest abstraction for "ship a model as a binary."
- **OpenAI Triton** (the language, not the server) — A GPU kernel DSL. Every major inference engine emits Triton kernels. The hidden compilation layer beneath the entire ecosystem.

---

*Next: [06-application-layer](06-application-layer.md). For how these engines run on Chinese hardware, see [07-chinese-hardware/03-software-stack](07-chinese-hardware/03-software-stack.md).*
