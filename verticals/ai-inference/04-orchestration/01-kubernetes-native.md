# Kubernetes-Native Orchestration

## What Goes In

A Kubernetes cluster. GPU Operator. A model and a way to serve it (vLLM, SGLang, etc.).

## What Comes Out

A `kubectl apply` later, a running inference service with autoscaling, load balancing, and (with caveats) production-grade behavior.

The Kubernetes-native stack is the "boring standard" answer. It is what most enterprise inference deployments look like. It is also architecturally inadequate for serious multi-node or disaggregated workloads, which is why the modern alternatives (Dynamo, llm-d) exist.

## The Boring Stack

The classic K8s-native inference stack:

- **KServe** (CNCF Incubating) — `InferenceService` Custom Resource Definition. Knative-based for serverless deployment, scale-to-zero via Knative Pod Autoscaler. Also supports Standard K8s (lightweight, no scale-to-zero) and **ModelMesh** (high-density, frequently-changing model serving).
- **vLLM or SGLang** as the underlying inference engine, running inside the InferenceService pod.
- **HPA** (Horizontal Pod Autoscaler) — scales replicas on CPU/metrics.
- **Cluster autoscaler** — provisions GPU nodes.

Recent KServe additions (2025-2026):

- **LLMInferenceService CRD** — first-class LLM serving primitive.
- **vLLM and llm-d backends** — native support.
- **OpenAI-compatible protocol** out of the box.
- **GPU support, model caching, KV cache offloading to CPU/disk**.
- **Request-based autoscaling** for generative workloads (different from CPU-based HPA).
- **Hugging Face native** — point at an HF repo, get a serving endpoint.
- Multi-model serving, canary rollouts, InferenceGraph (pipelines/ensembles), explainability (Alibi), payload logging, drift detection.

KServe repo: `github.com/kserve/kserve` — 5.7k stars, v0.19.0 (June 2026), Apache-2.0, Go 64% / Python 31%.

## Where the Boring Stack Breaks Down

Four architectural limitations that motivated the disaggregated approaches:

### 1. Gang scheduling

A multi-node tensor-parallel inference pod needs N GPUs atomically. Vanilla K8s scheduler can leave it stuck at 6/8 GPUs forever, blocking other workloads. **Fix**: Volcano, Kueue, YuniKorn, or Run:ai gang schedulers.

### 2. KV cache locality across pods

HPA scales replicas horizontally, but a request can land on a replica with cold KV cache. There is no cross-pod awareness of cache state. **Fix**: KV-aware routing (Dynamo's Smart Router, llm-d's predicted-latency scheduler).

### 3. Prefill/decode disaggregation

Not expressible in a single Kubernetes Deployment. Prefill and decode want different GPU pools with different sizing. **Fix**: Dynamo's disaggregated P/D, Mooncake's split architecture, llm-d's P/D support.

### 4. Cold-starts

GPU node boot + model weight load = minutes. HPA reacts too slowly. **Fix**: snapshot/restore (Tensorizer, ModelExpress), predictive autoscaling, keep-warm pools.

## The Modern Answers

### llm-d — The CNCF Coalition's Response

**Repo**: `github.com/llm-d/llm-d` — 3.8k stars, v0.8.1 (June 2026), Apache-2.0. **CNCF Sandbox** (March 2026).

Founded by **Red Hat, Google Cloud, IBM Research, CoreWeave, NVIDIA**. Supporters include AMD, Cisco, Hugging Face, Intel, Lambda, Mistral AI, UC Berkeley, U Chicago. This is the K8s-native community's coordinated counter to Dynamo.

**Architecture** (orchestrates **above** model servers like vLLM, SGLang):

- Intelligent prefix-cache + load-aware routing with experimental **predicted-latency scheduling**.
- **Tiered KV-cache offloading to CPU/disk** with global indexing.
- **Prefill/decode disaggregation + wide expert parallelism**.
- **SLO-aware autoscaling** based on real-time inference signals.
- OpenAI-compatible Batch APIs.
- v0.5 (Feb 2026) added active-active HA, UCCL-based transport resilience, scale-to-zero autoscaling, cache-aware LoRA routing.

**Verified performance highlights**:

- 3× output throughput + 2× TTFT vs round-robin (Llama 3.1 70B on 4× MI300X, Tesla/Red Hat).
- 40% TTFT/ITL reduction with predicted-latency scheduling (Google).
- Up to 70% higher tok/s with PD-disaggregation on gpt-oss / B200 (AWS).
- **50k tok/s cluster throughput** with wide-EP on 16×16 B200 (~3.1k tok/s/GPU).
- 13.9× throughput with hierarchical KV offloading at 250 concurrent users (4× H100).

### Seldon Core 2 — The Enterprise Play

**Repo**: `github.com/SeldonIO/seldon-core` (v2 branch) — 4.8k stars, v2.10.2 (December 2025). **License change**: Apache-2.0 → **Business Source License (BSL)** in v2.

**Architecture changes vs Core 1**:

- Core 1 = CRD + SeldonDeployments + istio/ambassador.
- Core 2 = **Kafka-based pipelines** for real-time data streaming between components.
- **Servers / Models / Pipelines / Experiments** as first-class CRDs.
- Multi-Model Serving with **overcommit** (deploy more models than memory allows — swap-in on demand).
- A/B tests, shadow deployments via Experiments.
- Influenced by their position paper "Desiderata for next generation of ML model serving" (arXiv 2210.14665).
- Language split: Go 55% / Kotlin 13% / JS 3% — Kotlin presence signals a JVM-based MLServer (V2 protocol).

Seldon has repositioned around "AI governance infrastructure" — safety, red-teaming, audit trails. A different angle than KServe's or llm-d's.

### BentoML / Yatai / BentoCloud

**Repo**: `github.com/bentoml/BentoML` — 8.7k stars, v1.4.39 (May 2026), Apache-2.0, Python 97%.

- **BentoML** = Python library for online serving + the **Bento** packaging artifact (`bentoml build` → standardized deployable unit → `bentoml containerize` → Docker image).
- `@bentoml.service` decorator + `@bentoml.api(batchable=True)` for adaptive batching.
- **Yatai** (historical) = K8s control plane for Bentos.
- **BentoCloud** = managed deployment, autoscaling, scale-to-zero.
- Differentiators: framework-agnostic, multi-model pipelines / inference graphs, workers/model parallelization, distributed serving systems.
- Examples ship as BentoVLLM, BentoDiffusion, BentoWhisperX etc.
- BentoML positions itself as the *packaging/SDK layer*, not a deep inference-engine optimizer — it runs on top of vLLM etc.

## The Gateway API Inference Extension (GAIE)

The Kubernetes community's response to the limitations of plain HPA + Ingress for inference workloads. **GAIE** (`kubernetes-sigs/gateway-api-inference-extension`) adds inference-specific primitives to the Kubernetes Gateway API: KV-aware routing, model-aware load balancing, prefill/decode splitting, all expressed as standard K8s resources.

Still maturing in 2026, but this is the path through which the disaggregated patterns (Dynamo, llm-d) will eventually be expressible in native K8s.

## When to Use the K8s-Native Stack

| Workload | Right choice |
|---|---|
| Single-model inference, low RPS | KServe + vLLM + HPA |
| Many small models, frequent swaps | KServe with ModelMesh |
| Multi-vendor hardware (NVIDIA + AMD + Intel) | llm-d or OpenShift AI |
| Enterprise governance / audit requirements | Seldon Core 2 |
| Python-native devex | BentoML + BentoCloud |
| Multi-node, KV-locality-sensitive | Use Dynamo or llm-d, not plain KServe |

## The Stalwart

**KServe** is the Stalwart of this layer. A CNCF Incubating project, Knative-based, with a clean `InferenceService` CRD that turned "I want to serve a model" into a Kubernetes-native resource. Scale-to-zero, autoscaling 5× faster than raw HPA per community benchmarks. It is the boring, standard choice — which is exactly what you want from infrastructure.

The hidden Stalwart here is **llm-d**, the CNCF Sandbox coalition project that finally gives the open-source community a coordinated answer to NVIDIA's Dynamo. Whether llm-d wins or Dynamo wins, the existence of both ensures no single vendor controls the orchestration layer.

---

*Next: [02-ray](02-ray.md)*
