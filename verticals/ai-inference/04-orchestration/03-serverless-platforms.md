# Serverless Inference Platforms

## What Goes In

A model. A way to package it. A request stream that is bursty and unpredictable.

## What Comes Out

An inference endpoint that scales to zero when idle, bills per second or per invocation, and requires no Kubernetes knowledge from the user.

Serverless inference platforms — Modal, Baseten, Replicate, Beam — are the closest thing to "AWS Lambda for GPUs." They trade raw performance for developer experience and per-second cost economics.

## The Pattern

All serverless inference platforms share the same architecture, with variations:

- A **custom container scheduler** over GPUs (not Kubernetes — usually too slow).
- A **packaging primitive** for models — Baseten has Truss, Replicate has Cog, Beam has beta9, Modal has its Python SDK.
- **Scale-to-zero** when idle.
- **Per-second (or sub-second) billing** based on actual GPU usage.
- **Cold-start engineering** — snapshot/restore, keep-warm pools, model-streaming — to mitigate the "70B model takes 90 seconds to load" problem.
- **Multi-cloud** under the hood to escape hyperscaler lock-in.

## Modal

The serverless-per-second platform. Architectural details partly UNVERIFIED.

- **Per-second billing** backed by a custom scheduler over containers.
- **Snapshot/restore** for cold-start reduction (Checkpoint/Restore-based container persistence, not full VM migration).
- Storage layer is volume-backed (Network File System style), not purely Redis.
- CLI/SDK is Python-decorator-driven (`@stub.function`).
- Ray-like primitives in the SDK (`.map`, `.spawn`, `stub.function`) — but Modal is **not** built on Ray.
- **UNVERIFIED**: whether they use gVisor, Firecracker, or a custom sandbox.

Modal's positioning is "serverless Python compute that happens to have GPUs," not "managed inference platform." A useful distinction — Modal is the choice for teams that want to ship bursty Python workloads (which may include inference) without managing infra.

## Baseten

The most NVIDIA-Dynamo-native production deployment.

- **Packaging**: **Truss** (open-source, pypi) wraps models into deployable containers from a `config.yaml` (model + hardware + engine). `truss push` builds and deploys.
- **Engines** (proprietary):
  - Engine-Builder-LLM (TensorRT-LLM compilation)
  - BIS-LLM (MoE models like DeepSeek R1 with KV-aware routing)
  - BEI (embeddings, up to 1400 client embeddings/s)
  - Chains framework orchestrates multi-step pipelines (RAG)
- **Topology**: Multi-cloud Capacity Management (MCM) schedules across providers/regions.
- **Deployment options**: Baseten Cloud, **Self-hosted (BYOC in customer cloud accounts)**, Hybrid.
- SOC 2 Type II, HIPAA.
- Uses **NVIDIA Dynamo** (confirmed via Run:ai docs listing "Dynamo" as supported workload type).
- **Verified benchmark (Qwen3-Coder 480B A35B, 262K context)**: KV-aware routing ON vs random routing: −50% TTFT, −34% TPOT, 89% cache hit rate, +61% RPS, +62% output TPS, −48% P95 latency, −49% P99 latency.

Baseten's customer list is the who's who of AI-native apps: Cursor, ClickUp, Decagon, Descript, Harvey, Notion, Patreon, Writer, Sourcegraph, Zed, Amp, Hubspot.

## Replicate

- **Cog** (Apache-2.0, 9.4k stars, `github.com/replicate/cog`): packages ML models into Docker containers. `cog.yaml` defines environment; a `BaseRunner` Python class defines setup/run; Cog generates an OpenAPI schema and serves a high-performance **Rust/Axum HTTP inference server**. Languages: Go 61.5%, Rust 17.6%, Python 5.8%. Handles CUDA/cuDNN/PyTorch compatibility automatically.
- Created by Andreas Jansson (ex-Spotify ML deploy) and Ben Firshman (Docker Compose author).
- **Virtualization**: pure container-level — no GPU slicing.
- Per-second billing is implementation-private.
- Cold-start engineering (the "fast booting fine-tune" feature) — implementation private.
- SkyPilot roots acknowledged historically; current internal deploy stack is not open.

Replicate's positioning is "deploy any model in one command" — the choice for indie developers and small teams who want a model live in 5 minutes.

## Beam (beta9)

- **Open source**: `github.com/beam-cloud/beta9` (AGPL-3.0, 1.7k stars) — the engine powering beam.cloud. Go 79.8%, Python 19.2%. Self-hostable.
- **Architecture**: "Custom container runtime, scheduler, and embedded caching" delivering **<1 second cold starts**. Scale-to-zero, QueueDepthAutoscaler, volume storage, BYO GPU support.
- Sandbox primitive = isolated containers for LLM-generated code.
- Architecturally closest to Modal among the providers profiled.

Beam's open-source approach (AGPL-3.0, self-hostable) is the structural differentiator vs Modal and Replicate. If you want to run "Modal-on-your-own-metal," Beam is the answer.

## The Cold-Start Problem

The defining engineering challenge of serverless inference. A 70B model on vLLM can take 30–120 seconds to load from disk/remote. That is catastrophic for synchronous autoscaling — the user has clicked away before the model is ready.

Mitigations:

- **Snapshot/restore** — CRIU (Checkpoint/Restore In Userspace) for containers. Modal and Beam both use variants of this.
- **Model weight streaming** — CoreWeave's Tensorizer (5 GB/s), NVIDIA's ModelExpress (7× faster cold-start).
- **Keep-warm pools** — maintain N warm replicas even at zero traffic. Kills the cost savings of scale-to-zero, but eliminates cold-starts.
- **Predictive autoscaling** — anticipate demand from leading indicators.
- **Model weight pre-fetching** to local SSD during low-demand periods.

The cold-start problem is the main reason serverless inference is still not the default for production workloads. The platforms that solve it best (Modal, Baseten) have the strongest customer traction.

## When to Use Serverless

| Workload | Right choice |
|---|---|
| Bursty, unpredictable demand | Modal, Baseten |
| Indie developer / side project | Replicate |
| Multi-model pipelines | Baseten |
| Need to self-host | Beam |
| Always-on, predictable load | Don't use serverless — rent dedicated GPU |

## The Stalwart

**Cog** is the Stalwart of this layer. A clean abstraction — "package a model into a Docker container with an OpenAPI schema" — that has become the conceptual model for "model as a deployable artifact." The Rust/Axum HTTP server inside Cog is the kind of focused engineering that distinguishes a Stalwart.

The other Stalwart: **beta9** (Beam's open-source runtime). The fact that Beam open-sourced the engine powering their commercial product is structurally important — it means the serverless-GPU runtime pattern is now a commodity, not a moat.

---

*Next: [04-dynamo](04-dynamo.md)*
