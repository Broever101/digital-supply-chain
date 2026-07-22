# Layer 4: Orchestration

## How to Read This Chapter

This layer is structured by **orchestration pattern**, not by vendor. Each file covers one architectural approach, with the providers that exemplify it discussed as instances of the pattern.

The reviewer noted that earlier coverage conflated the orchestration layer with vendor marketing. This folder separates the **patterns** (architectural ideas that recur across vendors) from the **vendors** (the specific products that ship those patterns).

## The Five Orchestration Patterns

Five distinct patterns are visible across the 2026 landscape:

| Pattern | Orchestrator substrate | KV cache locality | Multi-node story | Exemplars |
|---|---|---|---|---|
| **K8s-native (CRD-driven)** | Kubernetes + Knative/Gateway API + HPA | Per-pod, weak across pods | TP within pod only | KServe, Seldon Core 2, llm-d |
| **Ray-native** | Ray cluster + actors | Per-replica | Ray placement groups | Ray Serve / Anyscale |
| **Serverless container** | Custom scheduler over containers | None (stateless containers) | Single container per GPU | Modal, Replicate, BentoCloud |
| **Disaggregated custom** | Custom control plane (Rust/C++/Go) over bare pods or Slurm | **First-class** — global KV pool | Pre/dec split, EP across nodes | Dynamo, Mooncake, DeepSeek prod |
| **Vertical single-engine proprietary** | Closed inference engine on managed infra | Internal, not disclosed | Engine handles it internally | Together, Fireworks, OpenAI, Anthropic |

**Key fault line**: the K8s-native pattern breaks down at multi-node gang scheduling, KV-cache locality across pods, and prefill/decode disaggregation. This is precisely the gap **Dynamo**, **llm-d**, and **Mooncake** were built to close. The **Gateway API Inference Extension (GAIE)** in Kubernetes SIG is the K8s community's response.

## The Strategic Fault Line — Two Open-Source Coalitions

The most important thing to understand about 2026 orchestration: there are **two open-source coalitions competing to be the K8s-native standard**.

- **NVIDIA Dynamo** — Rust + Python + Go, vendor-led, NVL72-optimized, Apache-2.0, in the `ai-dynamo` GitHub org. The "we coordinate vLLM/SGLang/TRT-LLM across nodes" thesis.
- **llm-d** — Red Hat + Google Cloud + IBM Research + CoreWeave + NVIDIA (sitting in both camps) + AMD + Cisco + Hugging Face + Intel + Lambda + Mistral AI + UC Berkeley + U Chicago. CNCF Sandbox (March 2026). The multi-vendor coalition's coordinated counter to Dynamo.

Both projects have shipped production results in 2026. Both have adopters. NVIDIA is in both — a hedge that suggests the company wants to win either way.

## The Dominant Architecture of 2026 — Disaggregated Prefill/Decode

The single architectural pattern that defines 2026 SOTA inference at scale:

**Run prefill and decode on separate GPU pools, with KV cache transferred between them over RDMA.**

- Prefill is compute-bound; benefits from a big batch.
- Decode is memory-bandwidth-bound; wants small batches and lots of memory.
- Doing both on the same GPU forces compromises.
- Doing them separately lets neither be compromised.

This pattern was codified in the Mooncake paper (FAST 2025 Best Paper) and is now in vLLM, SGLang, TensorRT-LLM, Dynamo, llm-d, and LMDeploy. Treat it as the baseline, not as one option among many.

## Files in This Layer

| File | What it covers |
|---|---|
| [01-kubernetes-native.md](01-kubernetes-native.md) | The K8s "boring stack" (KServe + vLLM + HPA), its limitations, and the modern answers (llm-d, Seldon Core 2). |
| [02-ray.md](02-ray.md) | Ray Serve, KubeRay, Anyscale. The Python-native distributed-computing alternative. |
| [03-serverless-platforms.md](03-serverless-platforms.md) | Modal, Baseten, Replicate (Cog), Beam (beta9). The per-second/per-invocation tier. |
| [04-dynamo.md](04-dynamo.md) | NVIDIA Dynamo deep dive — the headline 2025 release. Rust + Python + Go. Smart Router, Planner, KVBM, ModelExpress, Grove. |
| [05-mooncake.md](05-mooncake.md) | Mooncake — the FAST 2025 Best Paper architecture that defines the disaggregated P/D pattern. Production at Kimi/Moonshot AI. |
| [06-vendor-stacks.md](06-vendor-stacks.md) | Together AI, Fireworks, xAI Colossus, DeepSeek, OpenAI/Anthropic. The proprietary stacks. |
| [07-cluster-schedulers.md](07-cluster-schedulers.md) | Kubernetes scheduling primitives (Volcano, Kueue, YuniKorn), NVIDIA Run:ai, and the gang-scheduling problem for distributed inference. |

## The Single Most Important Strategic Observation

The K8s-native "boring stack" (KServe + vLLM + HPA + cluster-autoscaler) is real but architecturally inadequate for any serious multi-node, KV-locality-sensitive, or disaggregated workload. If you are starting a production inference platform in 2026, you are choosing between **Dynamo**, **llm-d**, or **rolling your own disaggregated architecture on top of Mooncake primitives**. The K8s-native stack remains the answer for simple single-GPU-model workloads, but it does not scale to the modern frontier.

## The Stalwart

**Mooncake's Transfer Engine** is the Stalwart of this layer — quietly becoming the universal KV-cache transport layer, integrated into vLLM v1, SGLang, TensorRT-LLM, LMDeploy, LMCache, vLLM-Ascend, and NIXL. The 87/190 GB/s numbers it achieves on commodity RoCE fabric are the most important data-point in the disaggregated-inference era. A small team at Moonshot AI / Tsinghua built the substrate that the entire industry now depends on.

---

*Next layer: [05-inference-engines](../05-inference-engines.md)*
