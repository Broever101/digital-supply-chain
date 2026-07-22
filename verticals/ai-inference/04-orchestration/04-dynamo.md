# NVIDIA Dynamo — The Headline 2025 Release

## What Goes In

A cluster of GPUs (often an NVL72 rack). A set of inference engines (vLLM, SGLang, TensorRT-LLM). A request stream.

## What Comes Out

A coordinated multi-node inference system that treats KV cache as a first-class object, disaggregates prefill from decode, and routes requests to maximize cache hits.

NVIDIA Dynamo is the most important 2025 release in this layer. It is NVIDIA's play to own the orchestration of disaggregated inference — the architecture that the 2026 SOTA (Mooncake, DeepSeek-V3, llm-d) has converged on.

## Repository

`github.com/ai-dynamo/dynamo` — 7.5k stars, v1.2.1 (June 2026), Apache-2.0.

**Language split**: Rust 53% / Python 33% / Go 12%. Rust handles the hot paths (frontend HTTP, router, KV event distribution, inter-component messaging). Python wraps inference-engine bindings. Go handles the Kubernetes operator and Grove gang scheduling.

## Positioning

Explicitly "the orchestration layer **above** inference engines — it doesn't replace SGLang, TRT-LLM, or vLLM, it turns them into a coordinated multi-node inference system."

This is a critical framing. Dynamo is not another inference engine. It is the layer that decides which engine gets which request, on which GPU, with which KV cache state. The engines still emit tokens; Dynamo coordinates them.

## The Four Pillars

### Pillar 1: Smart Router (KV-aware routing)

The architectural centerpiece. How it works:

- Hashes incoming requests.
- Tracks KV locations in a **Radix Tree** across the whole fleet (a la SGLang's RadixAttention, but at fleet scale).
- Computes an overlap score between request and active KV blocks on all GPUs.
- Routes to maximize cache hit while balancing load.
- Replaces round-robin/load-based routing.

The router has a `--no-router-kv-events` mode that uses prediction instead of event-backed cache-state tracking — useful when you want zero external dependencies.

### Pillar 2: Planner (SLA-based autoscaler)

Continuously monitors:

- GPU capacity metrics.
- TTFT (Time To First Token) SLOs.
- ITL (Inter-Token Latency) SLOs.

Decides per-request whether to serve disaggregated vs aggregated. Shifts GPUs between prefill/decode pools dynamically. This is a fundamentally different autoscaling primitive than HPA (which scales replica count on metrics) — Dynamo scales *behavior*, not just capacity.

### Pillar 3: Distributed KV Cache Manager (KVBM)

**KVBM (KV Block Manager)** is the multi-tier KV cache offload system:

- GPU HBM (fastest, smallest)
- CPU DRAM (10× larger, ~10× slower)
- Local SSD (NVMe, 100× larger, ~100× slower)
- Shared network storage (S3/Azure Blob — added in 1.0)

Tiered eviction policies. Extends effective context beyond GPU memory; petabyte-scale storage at a fraction of GPU memory cost.

### Pillar 4: NIXL (NVIDIA Inference Transfer Library)

The underlying transport. `github.com/ai-dynamo/nixl`, v1.3.1. The abstraction Dynamo and Mooncake both plug into for cross-node KV cache transfer. Supports TCP, RDMA, AWS EFA, NVMe-oF, NVLink, CXL, and Ascend transports.

## Additional Components

### ModelExpress

`github.com/ai-dynamo/modelexpress` — streams weights GPU-to-GPU via NIXL/NVLink for **7× faster cold-start** (verified on DeepSeek-V3 on H200).

### Grove

`github.com/ai-dynamo/grove` — K8s operator for **topology-aware gang scheduling**. Places workloads across racks/hosts/NUMA, purpose-built for NVL72. This is critical because NVL72 changes the unit of scheduling from "8 GPUs in a node" to "72 GPUs in a rack."

### AIConfigurator

Simulates 10K+ deployment configs in seconds. Useful for capacity planning.

### Fault tolerance

Canary health checks + in-flight request migration ("workers fail; user requests don't"). This is non-trivial — most inference systems still lose in-flight requests when a worker dies.

## Routing Topologies

Two ways to expose Dynamo:

- **Dynamo-native Frontend**: `client → Frontend → Router → workers`.
- **Gateway API + GAIE** (Gateway API Inference Extension): `client → Gateway → EPP plugin → Frontend sidecar direct → workers`. For K8s-standard shops.

**No etcd/NATS required** on K8s — uses native CRDs + EndpointSlices. TCP request plane.

## Production Results (Verified)

From the README "Key Results" section:

- **7× throughput/GPU on DeepSeek R1 / GB200 NVL72** (SemiAnalysis InferenceX benchmark).
- **2× faster TTFT** (Baseten, Qwen3-Coder 480B).
- **80% fewer SLA breaches at 5% lower TCO** (Alibaba APSARA 2025).
- **750× throughput on DeepSeek-R1 / GB300 NVL72** (InferenceXv2).
- **Mistral Large 3 and Kimi K2**: 10× inference speedup on GB200.

**1.0 shipped March 2026** with:

- Zero-config **DGDR (DynamoGraphDeploymentRequest)** — model + hardware + SLA in one YAML.
- Multimodal E/P/D.
- FastVideo/SGLang Diffusion video support.

## DeepSeek-R1 Reference Deployment

The flagship Dynamo deployment:

- **Prefill**: EP4 × DP16 (4-way expert parallel × 16-way data parallel).
- **Decode**: EP64 × DP3 (64-way expert parallel × 3-way data parallel).
- Up to 30× requests served at launch (NVIDIA dev blog).
- 7× throughput per GPU (SemiAnalysis InferenceX).

This is the disaggregated prefill/decode pattern with wide expert parallelism, made operational.

## The Deep Architecture

**Rust core** handles hot paths: Frontend HTTP server, Router (Radix Tree overlap scoring), KV event distribution, inter-component TCP messaging.

**Python** (33%) wraps inference-engine bindings (vLLM/SGLang/TRT-LLM workers) and extensible policies.

**Go** (12%) handles K8s operator + Grove gang scheduling.

**Service discovery is K8s-native** (CRDs + EndpointSlices). Explicitly no etcd or NATS required for standard K8s deploys, though both are supported for Slurm/bare-metal. Request plane is TCP.

## Why This Matters

Three reasons:

1. **Dynamo is NVIDIA's play to remain architecturally central even as the open-source ecosystem matures.** By being Rust + Python and Apache-2.0, NVIDIA positions Dynamo as the open standard for hyperscale inference coordination. Whether the market adopts it or continues to roll its own (Together, Fireworks, Anyscale all have proprietary equivalents) will define the next 18 months.

2. **The disaggregated P/D pattern it exemplifies is the new architecture floor.** If you are not running PD-disaggregation at hyperscale, you are leaving 2–4× throughput on the table.

3. **NVIDIA is in both Dynamo and llm-d.** The company is hedging — Dynamo is the vendor-led play; llm-d is the CNCF coalition play. NVIDIA wins either way.

## The Stalwart

The Stalwart of this layer is not Dynamo itself — it is **NIXL** (the NVIDIA Inference Transfer Library). Small, focused, doing one job exceptionally well: cross-node KV cache transport. Integrated into Dynamo, Mooncake, vLLM v1, SGLang, TensorRT-LLM, LMDeploy, and LMCache. The kind of library that becomes invisible because it just works. The hidden layer beneath the disaggregated-inference era.

---

*Next: [05-mooncake](05-mooncake.md)*
