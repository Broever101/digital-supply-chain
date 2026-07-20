# Layer 3: Orchestration

## What Goes In

A cluster of GPU instances — bare metal or virtualized — connected by a fabric like NVLink, InfiniBand, or Ethernet.

## What Comes Out

A self-driving inference platform: requests come in from an API gateway, get scheduled onto the right GPU, scale up under load, scale to zero when idle, and stay inside latency SLOs while doing it.

This is the layer that turns hardware into a service. Without it, you have a pile of GPUs. With it, you have an inference platform.

## Who Does It

### The Kubernetes Foundation

Almost every modern inference platform runs on Kubernetes. The vanilla distribution plus a few key add-ons has become the default substrate.

- **NVIDIA k8s-device-plugin** and the broader **GPU Operator** — The base layer that exposes GPUs to Kubernetes workloads. Without this, Kubernetes does not know a GPU exists.
- **Vanilla Kubernetes scheduler** — Adequate for stateless workloads but has no gang scheduling. Known to strand GPUs under contention: a distributed inference pod that needs 8 GPUs may get 6, then sit waiting forever for the last 2.
- **Volcano** (CNCF, Huawei-led) — Batch and gang-scheduling for Kubernetes.
- **Kueue** (SIG-Scheduling) — Now default in K8s 1.30+ for batch workloads.
- **Apache YuniKorn** — YARN-Kubernetes hybrid scheduling, useful for orgs with HPC heritage.
- **Grove** (part of NVIDIA Dynamo) — New. Topology-aware gang scheduling for NVL72 racks. Important because NVL72 changes the unit of scheduling from "8 GPUs in a node" to "72 GPUs in a rack."

### AI/ML Workload Schedulers

Software purpose-built for GPU cluster sharing. The category consolidated sharply in 2024–25.

- **Run:ai** — Acquired by NVIDIA in April 2024 for ~$700 million. MIG-aware fractional-GPU scheduling, gang scheduling, fair-share. NVIDIA has rebranded it as "NVIDIA Run:ai" and is integrating it into the AI Enterprise stack. The de-facto standard for AI cluster sharing.
- **Domino Data Lab, Kaleido, cnvrg (Intel)** — Enterprise MLOps platforms. No recent breakout signal in 2025–26 coverage. The category lost altitude as Kubernetes-native tooling absorbed their function.

### Ray and Anyscale

Ray is a distributed computing framework from UC Berkeley's RISELab lineage. It is the dominant way to scale Python workloads — including inference — beyond a single machine.

- **Ray Serve** — Production model serving with autoscaling. Widely used for offline batch inference at scale (ByteDance's 200TB multi-modal case is the canonical example).
- **KubeRay** — The Kubernetes operator for Ray clusters. The recommended way to run Ray on Kubernetes.
- **RayLLM / Aviary** — Aviary was renamed RayLLM and folded into the Ray ecosystem. Anyscale still ships the `anyscale/ray-llm` Docker image. Effectively legacy now that vLLM and SGLang dominate.
- **Anyscale** — The company behind Ray. Fully-managed Ray, plus the Anyscale Endpoints inference API (now somewhat deprecated as a standalone product).

### Inference-Specific Orchestrators

These are CRDs (Custom Resource Definitions) and platforms purpose-built for serving models.

- **KServe** (CNCF Incubating) — Knative-based, scale-to-zero, `InferenceService` CRD. Strong for serverless inference. Community benchmarks claim autoscaling 5× faster than raw HPA. Transitioned from LFAI to CNCF. The "boring, standard" choice.
- **Seldon Core 2** — K8s-native, repositioned around "AI governance infrastructure" in 2025. Supports vLLM and Hugging Face backends.
- **BentoML / Yatai / BentoCloud** — BentoML is a Python-native model packager (the "Bento" archive format). Yatai is the Kubernetes deployment control plane. BentoCloud is the managed version.
- **Modal** — Serverless Python compute, not inference-specific, but heavily used for inference. Strong DX. Oracle Cloud partnership for GPU pricing.
- **Baseten** — Managed inference platform. Publicly known NVIDIA Dynamo user (2× TTFT blog).
- **Beam** (beam.cloud) — Open-sourced core runtime, Modal-clone DX, multi-cloud.
- **Replicate** — Uses **Cog** (open-source model packager) and has SkyPilot roots. Internal engine not disclosed.

### Vendor Stacks

The big vendors have their own end-to-end stacks. The most important 2025 entry is NVIDIA Dynamo.

- **NVIDIA NIM** — Containerized inference microservices. Model weights + runtime + monitoring in one Docker image. ~100+ models supported. License is NVIDIA AI Enterprise (commercial). The engines inside are TensorRT-LLM and vLLM. The packaging is the product.
- **NVIDIA Dynamo** (ai-dynamo/dynamo, 7.5k GitHub stars, Apache-2.0) — The headline 2025 release. **Rust + Python.** Explicitly positioned as the orchestration layer *above* engines — does not replace vLLM, SGLang, or TensorRT-LLM, but coordinates them across nodes. Key features:
  - **Disaggregated prefill/decode** — run prefill and decode on separate GPU pools, with KV cache transferred between them.
  - **KV-aware routing** — route requests to GPUs that already have relevant KV cache, dramatically improving TTFT for agentic and RAG workloads.
  - **KVBM (KV Block Manager)** — GPU → CPU → SSD offload of KV cache, expanding effective capacity.
  - **ModelExpress** — 7× faster cold-starts.
  - **SLA-based Planner autoscaler** — autoscale based on latency SLOs, not just CPU/memory.
  - Verified production claims: **7× throughput per GPU on DeepSeek-R1 over GB200 NVL72** (SemiAnalysis InferenceX benchmark), **2× TTFT improvement** (Baseten), **80% fewer SLA breaches** at 5% lower TCO (Alibaba APSARA 2025).
- **NVIDIA Triton Inference Server** — Now branded **Dynamo-Triton**. Backend-agnostic (TensorRT, PyTorch, ONNX, OpenVINO). Multi-model serving on one process — picks up where dedicated LLM engines stop (rerankers, embedders, preprocessors).
- **NVIDIA TensorRT** — The closed-ish compiler under TensorRT-LLM. Important infrastructure, not a user-facing product.

### Compilers and Runtimes (Adjacent)

Not orchestrators per se, but the compilation targets every engine leans on.

- **ONNX Runtime** (Microsoft) — Cross-platform ML runtime. The enterprise "interoperable" default.
- **Apache TVM** and **MLC-LLM** — Machine learning compilation. MLC-LLM is the foundation for Apple MLX's compilation path and several edge inference projects.
- **OpenXLA / PJRT / StableHLO** — Google's compiler stack. The path from PyFlow / JAX to silicon.
- **OpenAI Triton** — *The language*, not a server. A GPU kernel DSL. vLLM, TensorRT-LLM, FlashAttention, and SGLang's sgl-kernel all emit Triton kernels. The hidden layer beneath the engines.

## Battle Stories — Verified Production Users

- **Together AI** — Custom "Together Inference Engine 2.0" built on FlashAttention-3, custom GEMM/MHA kernels, proprietary quantization, speculative decoding. Reports **400+ tok/s on Llama 3 8B**. Built by the FlashAttention team (Tri Dao).
- **Fireworks AI** — Proprietary engine. "Day-0" model support is the marketing wedge; underlying tech not publicly disclosed.
- **Databricks, Snowflake, NAVER Place, Microsoft Bing** — Publicly cited TensorRT-LLM users in NVIDIA developer blogs 2024–2025. Bing's "Transition to LLM/SLM Models" blog (January 2025) details TensorRT-LLM in production search.
- **SGLang production adopters** (per SGLang README): **xAI, AMD, NVIDIA, Intel, LinkedIn, Cursor, Oracle Cloud, Google Cloud, Microsoft Azure, AWS, Atlas Cloud, Voltage Park, Nebius, DataCrunch, Novita, Modal, Baseten**, plus Berkeley, Stanford, UCLA, UW, Tsinghua. xAI uses SGLang for Grok 2.5.
- **Mooncake** — Production platform for **Kimi / Moonshot AI**. Kimi K2 deployed on 128× H200 with 224k prefill tok/s and 288k decode tok/s (July 2025 blog). Integrated into SGLang, vLLM, TensorRT-LLM, vLLM-Ascend, LMDeploy, LMCache, NIXL.
- **Dynamo** — Baseten (2× TTFT, public blog), Alibaba (80% fewer SLA breaches at 5% lower TCO, APSARA 2025), Mistral (10× faster Mistral Large 3 on GB200), Kimi K2 (10× speedup), Dell + WEKA (storage-tier KV).
- **IBM watsonx.ai** — Historically TGI; with TGI archived in March 2026, future stack unknown.

OpenAI and Anthropic: no public evidence either uses TensorRT-LLM. Both almost certainly run custom stacks. Treat any such claim as unverified.

## Key Trends

**Dynamo is the most important 2025 release in this layer.** It is NVIDIA's play to own the orchestration of disaggregated inference — the architecture that the 2026 SOTA (Mooncake, DeepSeek-V3) has converged on. By being Rust + Python and Apache-2.0, NVIDIA is positioning Dynamo as the open standard for hyperscale inference coordination. Whether the market adopts it or continues to roll its own (Together, Fireworks, Anyscale all have proprietary equivalents) will define the next 18 months.

**Disaggregated prefill/decode is the new baseline.** Prefill is compute-bound; decode is memory-bandwidth-bound. Running both on the same GPU forces compromises. Running them on separate GPU pools — with KV cache transferred over RDMA — lets neither be compromised. The Mooncake paper (FAST 2025 Best Paper) is the reference architecture. It is now in vLLM, SGLang, TensorRT-LLM, Dynamo, and LMDeploy.

**KV-aware routing may matter more than raw speed.** For agentic workloads where system prompts, tool schemas, and few-shot examples repeat, routing a request to the GPU that already has the relevant KV cache is a bigger win than any throughput optimization. Dynamo's KV-aware router and SGLang's RadixAttention both exploit this.

**Serverless inference is winning the small-workload tier.** Modal, Baseten, Replicate, Beam have proven that per-second billing with scale-to-zero is economically viable. The cold-start problem (70B models can take 30–120 seconds to load) is the remaining frontier. Dynamo's ModelExpress (7× faster cold-starts) is the leading solution.

**Kubernetes is not enough on its own.** The default scheduler strands GPUs. Gang scheduling (Volcano, Kueue), topology-aware scheduling (Grove), and GPU-aware fractional sharing (Run:ai) are all required for serious inference at scale. Anyone deploying inference to vanilla Kubernetes and expecting it to work is going to have a bad time.

## The Stalwart

**KServe** is the Stalwart of this layer. A CNCF Incubating project, Knative-based, with a clean `InferenceService` CRD that turns "I want to serve a model" into a Kubernetes-native resource. Scale-to-zero, autoscaling 5× faster than raw HPA. It is the boring, standard choice — which is exactly what you want from infrastructure. The teams behind it are not loud on Twitter, but KServe is what runs under the hood at a surprising number of inference platforms.

The other Stalwart here is **NIXL** (NVIDIA Inference Transfer Library) — the abstraction that Dynamo and Mooncake both plug into for cross-node KV cache transfer. Small, focused, doing one job exceptionally well. The kind of library that becomes invisible because it just works.

---

*Next: [05-inference-engines](05-inference-engines.md)*
