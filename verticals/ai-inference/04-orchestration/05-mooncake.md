# Mooncake — The Disaggregated KVCache Architecture

## What Goes In

A cluster of GPUs with underutilized CPU, DRAM, and SSD resources. A high-volume inference workload (especially long-context or agentic).

## What Comes Out

A serving architecture where the KV cache is a first-class, globally-addressable object that flows between prefill and decode pools, delivering 4–6× throughput improvements over monolithic serving.

Mooncake is the **FAST 2025 Best Paper** architecture. It is the reference design for the disaggregated prefill/decode pattern that now defines SOTA inference. Every major inference engine has since implemented some variant of it.

## Paper and Repository

- **Paper**: "Mooncake: A KVCache-centric Disaggregated Architecture for Moonshot AI's Kimi" (arXiv 2407.00079, FAST 2025 Best Paper, v4 September 2025).
- **Repo**: `github.com/kvcache-ai/Mooncake` — 5.9k stars, Apache-2.0, C++ 80% / Python 11%.
- **Production platform**: Kimi (Moonshot AI's chatbot). The Kimi K2 deployment is the canonical case study.

## The Core Intellectual Move

Treat KV cache not as engine-internal state but as a **disaggregated, globally-addressable object** stored in a pool built from the otherwise-wasted CPU/DRAM/SSD of the GPU cluster.

This single move unlocks four things:

1. **Prefix-cache reuse across prefill nodes** without redundant compute.
2. **Layer-wise streaming of KV** from prefill to decode, overlapped with computation.
3. **Hot-block replication and cold-block eviction** as a cache-management problem.
4. **Early rejection under overload** because you can predict whether a decode slot will exist.

## The Architecture

### Conductor — The Global Scheduler

For each request, Conductor picks a (prefill, decode) pair, runs a 4-step workflow:

1. **KVCache reuse** — load prefix cache from remote CPU memory.
2. **Incremental prefill**, with KV written back to CPU memory.
3. **KVCache transfer** via Messenger.
4. **Decode joins continuous batching**.

### Chunked Pipeline Parallelism (CPP) for Multi-Node Prefill

Rejects sequence parallelism (too much per-layer all-reduce) in favor of **pipeline-parallel chunks**, communicating only at stage boundaries. First application of training-pipeline ideas to inference.

### Layer-Wise Prefill

Async KV load/store overlapped per-layer with compute, so prefill time ≈ max(KV-load-time, compute-time). Makes prefill scheduling independent of VRAM size.

### KVCache-Centric Scheduling (Algorithm 1)

- Hash-based prefix matching across all prefill instances.
- Estimates TTFT = transfer + queue + prefill time per instance; picks min.
- Hot blocks get replicated to avoid fetch congestion.
- Cold blocks get swapped out.

### Early Rejection Policy

Unlike research systems that assume all requests complete, Mooncake **predicts future load and rejects requests early (HTTP 429)** if they'd violate SLO after prefill. Saves wasted prefill compute. Naive rejection caused load fluctuations; fixed by short-term load prediction.

This is non-obvious and important: most inference systems either accept everything (and degrade latency) or shed load indiscriminately. Mooncake's predictive rejection is the engineering refinement that makes the architecture viable.

### Transfer Engine (TE)

The load-bearing component.

- **RDMA-based**.
- **87 GB/s on 4×200 Gbps RoCE**, **190 GB/s on 8×400 Gbps RoCE** (2.4× and 4.6× faster than TCP).
- Topology-aware path selection (NUMA affinity).
- Multi-NIC bandwidth aggregation.
- Automatic failover.
- Supports TCP / RDMA / AWS EFA / NVMe-oF / NVLink / CXL / Ascend transports.

### Mooncake Store

Distributed KVCache + model-weight store on top of Transfer Engine. Multi-tier DRAM/SSD. Per-object replica/pin policies.

### Mooncake EP & PG (newer additions)

DeepEP-compatible fault-tolerant expert parallelism for MoE. Plus a PyTorch `torch.distributed` backend with rank-recovery primitives. Directly motivated by Kimi-K2-scale (1T-param) MoE fragility.

## Production at Kimi K2

The flagship deployment (July 2025 blog):

- **128× H200**.
- PD-disaggregated + large-scale expert parallelism.
- **224k prefill tok/s**.
- **288k decode tok/s**.

The original Mooncake paper claims:

- **+75% request capacity** under real Kimi workloads.
- Up to **+525% throughput** in simulated long-context scenarios.

## Ecosystem Integrations

All verified in the Mooncake README (July 2026 state):

- **SGLang**: PD-disagg, HiCache, Elastic EP, P2P weight transfer — 1T-param Kimi-K2 weights across thousands of GPUs in ~20s via checkpoint-engine.
- **vLLM v1**: MooncakeStoreConnector as a native KV Connector.
- **TensorRT-LLM**: cache_transmission/mooncake_utils.
- **vLLM-Ascend**.
- **LMDeploy**.
- **LMCache**.
- **NIXL** (May 2025 as a backend plugin).
- **TorchSpec**.
- **FlexKV** (Tencent + NVIDIA).

Mooncake's Transfer Engine is the most-adopted single component in the disaggregated-inference space.

## The Deep Architecture

**C++** (80%) for the Transfer Engine, KV cache management, and the high-throughput data plane. **Python** (11%) for orchestration, integration with PyTorch and inference engines.

The key engineering choice: **the KV cache pool is built from "wasted" cluster resources** — the CPU, DRAM, and SSD that every GPU server has, but that are typically underutilized during pure inference. Mooncake's insight is that these resources form a natural tier in a hierarchical KV cache.

This is "trading more storage for less computation" — and at Kimi scale, it works dramatically.

## Why Mooncake Matters

Three reasons:

1. **It defined the pattern.** Disaggregated prefill/decode + global KV cache pool is now the baseline architecture for SOTA inference at scale. Every major engine has implemented variants.
2. **The Transfer Engine has become the universal substrate.** Integrated into every major inference engine and orchestration framework. The most-adopted single component in the disaggregated-inference era.
3. **It is open-source.** Apache-2.0. A small team at Moonshot AI / Tsinghua gave away the architecture that defines the frontier.

## When to Use Mooncake

Mooncake is overkill for:

- Single-node inference workloads.
- Small-model serving.
- Workloads with no shared prefixes.

Mooncake is essential for:

- Hyperscale serving of long-context models.
- Agentic workloads with repeated system prompts.
- MoE models at scale (with the EP & PG extensions).
- Any deployment where KV cache size dominates memory budget.

## The Stalwart

**Mooncake Transfer Engine** is the Stalwart of the entire disaggregated-inference era. The 87/190 GB/s numbers it achieves on commodity RoCE fabric are the most important data-point in the field. A small team at Moonshot AI / Tsinghua built the substrate that the entire industry now depends on — and they open-sourced it under Apache-2.0.

The hidden Stalwart in this story is **Conductor's early rejection policy**. The engineering refinement that makes the whole architecture viable under load. Most coverage of Mooncake focuses on the throughput wins; the rejection policy is the operational insight that separates research from production.

---

*Next: [06-vendor-stacks](06-vendor-stacks.md)*
