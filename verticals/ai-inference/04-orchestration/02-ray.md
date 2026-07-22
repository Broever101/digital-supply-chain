# Ray-Native Orchestration

## What Goes In

A Python workload that needs to scale across many machines. A Ray cluster.

## What Comes Out

A model serving endpoint that scales horizontally, with the Python-native devex of writing function calls.

Ray is a distributed computing framework from UC Berkeley's RISELab lineage. It is the dominant way to scale Python workloads — including inference — beyond a single machine. Ray Serve is the inference-serving layer built on Ray.

## Ray Serve — The Architecture

Ray Serve is a framework-agnostic scalable serving library built on Ray. Differentiators vs K8s-native stacks:

- **Programmable model composition** — `DeploymentHandle.remote()` calls look like function calls but run across machines. No YAML static graph. (Explicit contrast vs Seldon/KServe/Cortex in the Ray Serve docs.)
- **Fractional GPU allocation** — share one GPU across many models for cost. Useful for multi-tenant inference of small models.
- **Dynamic request batching** — adaptive batching within Ray Serve, separate from engine-level continuous batching.
- **Multi-node/multi-GPU** — Ray placement groups handle the topology.
- **Streaming** — first-class streaming responses.
- **Autoscaling primitives** — replica-count autoscaling per deployment.
- **Native Kubernetes operator**: **KubeRay** — the recommended way to run Ray on Kubernetes.

Notably, the Ray Serve docs are explicit: "doesn't perform model-specific optimizations." Ray Serve delegates to vLLM, TensorRT-LLM, etc. for kernels. Ray handles scheduling and routing; the engines handle inference.

## Anyscale — The Company Behind Ray

The commercial entity behind Ray. Two product lines today:

- **Anyscale** (the platform) — fully-managed Ray, Anyscale Jobs (batch), Anyscale Services (long-running).
- **Anyscale Endpoints** (the LLM inference product) — somewhat deprecated post-2024. Anyscale repositioned around Managed Ray rather than hosted endpoints.

The strategic shift: Anyscale stopped competing with Together/Fireworks on hosted LLM inference and went back to selling Ray itself. The inference-API market commoditized faster than Anyscale could win it.

## Ray Strengths

- **Python-native**. No YAML, no CRDs, no K8s manifests. Just Python.
- **Great for ML researchers.** The same framework can do training, batch inference, and online inference.
- **Composable pipelines.** Multi-model inference graphs are first-class.
- **Fractional GPU allocation** for cost-efficient multi-tenant inference of small models.

## Ray Weaknesses

- **Not as efficient as K8s for plain serving.** Ray adds an actor layer that K8s-native serving avoids.
- **Operational complexity at scale.** Ray clusters have their own quirks; debugging distributed Ray is a skill.
- **The inference-engine integration is the user's responsibility.** Ray Serve does not provide vLLM or SGLang wrappers out of the box (though examples exist).
- **The disaggregated P/D pattern is not native to Ray.** You can build it, but you have to build it.

## Production Use Cases

Ray Serve shines for:

- **Offline batch inference at scale** — the canonical use case. ByteDance's 200TB multi-modal batch case is the example everyone cites.
- **Multi-model pipelines** — e.g., RAG systems where retriever + reranker + LLM all need to scale together.
- **Research and dev → production continuum** — teams that prototype in Ray can ship the same code.

It is less common for:

- **Single-model, high-throughput, latency-sensitive inference.** K8s-native (KServe) or disaggregated (Dynamo/llm-d) are usually better.
- **Frontier lab API serving.** No major frontier lab uses Ray for production inference serving (publicly disclosed).

## The Ray vs K8s Fault Line

The fundamental question: do you want to express your inference platform in **Kubernetes primitives** (CRDs, Deployments, Services) or in **Python code**?

- Pick Kubernetes if: you have a platform team, you want standard tooling (Helm, Argo, Kubecost), you have non-AI workloads too.
- Pick Ray if: you have ML engineers but not platform engineers, you need to ship fast, you have complex multi-model pipelines, your workload is more research-shaped than ops-shaped.

Most production inference platforms end up with both: Ray for research/batch, K8s for production online serving.

## KubeRay

The Kubernetes operator for Ray clusters. The recommended way to run Ray on Kubernetes. Lets you declare a Ray cluster as a CRD, with autoscaling and node affinity.

`github.com/ray-project/kuberay` — the official operator.

## The Stalwart

**Ray Serve** itself is the Stalwart — the only major distributed-computing framework for Python that has crossed into mainstream production use. Born from UC Berkeley research, maintained by Anyscale + community, Apache-2.0, 35k+ stars. It is the "if you want to scale Python without writing Kubernetes YAML, this is your tool" answer.

The hidden Stalwart: **Anyscale's open-source contributions** to Ray. Even as the commercial Anyscale Endpoints product wound down, Anyscale continues to invest heavily in Ray itself — a notable counter to the pattern where companies stop investing in OSS once the commercial pivot fails.

---

*Next: [03-serverless-platforms](03-serverless-platforms.md)*
