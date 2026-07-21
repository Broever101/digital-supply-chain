# Provider Profiles — How Specific Companies Actually Virtualize

## What Goes In

The abstractions from the previous files (MIG, BlueField DPU, fractional GPU, serverless containers).

## What Comes Out

Specific architectural profiles of how the leading AI clouds and serverless platforms actually slice, isolate, and bill GPUs in production.

The reviewer noted that earlier versions of this vertical conflated tech with business. This file is the technical bypass. Pricing and positioning live in [01-end-user-lens.md](../01-end-user-lens.md); the architecture lives here.

## NVIDIA — The Foundation

Every other profile is built on top of NVIDIA's primitives. The relevant summary (covered in detail in [02-gpu-sharing.md](02-gpu-sharing.md)):

- **MIG** — hardware partitioning (1/7 SM slices + 1/8 mem slices). A100, H100, H200, B200, GB200.
- **MPS** — logical, software partitioning. Up to 48 clients.
- **vGPU** — hypervisor-level, paired with VMware/Broadcom or Nutanix. Frame-buffer carving.
- **Time-slicing** — driver context-switching. Default on most clouds.
- **Confidential Computing** — hardware-isolated TEE on Hopper. Disables MIG (mutually exclusive).

The **NVIDIA AI Enterprise** license unlocks vGPU + Run:ai + DOCA + Operators. Current: Infra 8.1 (R595 driver, May 2026) and Infra 7.7 LTSB (R580, July 2026).

## CoreWeave — BlueField + EVPN VXLAN

The most architecturally interesting neo-cloud. Key technical points:

- **Not MIG, not vGPU.** Every customer gets dedicated whole GPUs.
- **Multi-tenancy via NVIDIA BlueField DPU** running DOCA. Per-customer virtual networks carved at line rate via EVPN VXLAN. The DPU is the trust boundary, not the hypervisor or the GPU.
- **BlueField-4** (October 2025): 6× compute, 800 Gb/s.
- **Bare-metal stack**: Kubernetes-native from day one. Products: CoreWeave Kubernetes Service (CKS), **SUNK** (Slurm-on-Kubernetes — topology-aware scheduling + continuous health management for 1000+ GPU jobs), Bare Metal servers, Mission Control (Fleet Lifecycle Controller, Node Lifecycle Controller, Observability).
- Uses **Kueue** for K8s-native training workload queueing.
- **Tensorizer** (MIT, 318 stars, `github.com/coreweave/tensorizer`): model weight streaming at ~5 GB/s wire-speed. Pre-tensorized models on `s3://tensorized/`.
- **Storage**: "AI Object Storage" (CAIOS) — 2+ GB/s per GPU throughput, 7+ GB/s/GPU on Blackwell Ultra.
- **Fabric**: NVIDIA Spectrum-X (Cumulus) per positioning; Spectrum-X / Quantum-2 InfiniBand on HGX deployments.

**The pattern**: BlueField DPU + EVPN VXLAN + dedicated whole-GPU is becoming the default sovereign-cloud isolation model. NVIDIA's DPF Operator productizes it for the broader market.

## Lambda — Managed Slurm

A simpler model than CoreWeave, focused on raw GPU rentals.

- **Bare-metal first.** Public Cloud offers 16× to 512× H100/B200 clusters via **1-Click Clusters** backed by **Managed Slurm** (not K8s by default). Managed Kubernetes and Preinstalled Kubernetes are options.
- **On-Demand single instances** for smaller workloads.
- **Private Cloud** = single-tenant 1000+ GPU clusters with low-level access.
- **MIG usage** is documented in education materials but the public offering exposes whole GPUs (no slicing).
- SkyPilot and dstack documented as orchestration options.
- Domain migrated lambdalabs.com → lambda.ai.

## Modal — Serverless-Per-Second

Architectural details are partly UNVERIFIED (Modal's docs portal blocked fetches). Known:

- **Per-second billing** backed by a custom scheduler over containers.
- **Snapshot/restore** for cold-start reduction (Checkpoint/Restore-based container persistence, not full VM migration).
- Storage layer is volume-backed (Network File System style), not purely Redis.
- CLI/SDK is Python-decorator-driven (`@stub.function`).
- Ray-like primitives in the SDK (`.map`, `.spawn`, `stub.function`) — Modal is **not** built on Ray.

**UNVERIFIED**: whether they use gVisor, Firecracker, or a custom sandbox. Public material is ambiguous.

## Baseten — Truss + Dynamo

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

## Replicate — Cog + Per-Second

- **Cog** (Apache-2.0, 9.4k stars, `github.com/replicate/cog`): packages ML models into Docker containers. `cog.yaml` defines environment; a `BaseRunner` Python class defines setup/run; Cog generates an OpenAPI schema and serves a high-performance **Rust/Axum HTTP inference server**. Languages: Go 61.5%, Rust 17.6%, Python 5.8%. Handles CUDA/cuDNN/PyTorch compatibility automatically.
- Created by Andreas Jansson (ex-Spotify ML deploy) and Ben Firshman (Docker Compose author).
- **Virtualization**: pure container-level — no GPU slicing.
- Per-second billing is implementation-private.
- Cold-start engineering (the "fast booting fine-tune" feature) — implementation private.
- SkyPilot roots acknowledged historically; current internal deploy stack is not open.

## Beam (beta9) — Open-Source Modal-Clone

- **Open source**: `github.com/beam-cloud/beta9` (AGPL-3.0, 1.7k stars) — the engine powering beam.cloud. Go 79.8%, Python 19.2%. Self-hostable.
- **Architecture**: "Custom container runtime, scheduler, and embedded caching" delivering **<1 second cold starts**. Scale-to-zero, QueueDepthAutoscaler, volume storage, BYO GPU support.
- Sandbox primitive = isolated containers for LLM-generated code.
- Architecturally closest to Modal among the providers profiled.

## RunPod / Vast.ai — Spot Marketplaces

Two-tier trust models; container-level isolation only.

- **RunPod**: **Secure Cloud** (T3/T4 data centers, vetted) vs **Community Cloud** (peer-to-peer vetted providers). Container-based: customer Docker image runs on a host; RunPod runs Docker for you (no Docker Compose, no UDP, no Windows). Network Volumes = persistent storage attachable to multiple Pods. Per-minute billing.
- **Vast.ai**: pure marketplace — hosts list machines (hobbyist through T4 datacenters), set prices; renters filter by GPU/RAM/CPU/bandwidth/reliability. `vastai` CLI/SDK. Templates (Docker images) for one-click deploys. Serverless endpoints + interruptible (spot) pricing.

**Isolation (Community tier)**: container-level only (cgroups + Docker device passthrough) — no hardware isolation between the renter and other tenants on the same host. This is the structural reason Community is cheaper and riskier.

## Hyperscaler Technical Profiles

### AWS Nitro System

- Purpose-built hardware + lightweight hypervisor.
- **Nitro Cards** (VPC, EBS, Instance Storage, Controller) offload IO.
- **Nitro Security Chip** minimizes attack surface.
- **Nitro Hypervisor** manages CPU/memory "indistinguishable from bare metal."
- **Nitro Isolation Engine** enforces tenant boundaries.
- Supports **bare-metal EC2** (BYO hypervisor or none) and **Nitro Enclaves** for confidential compute.
- GPU instances (P5, P6) pass GPUs through to the VM.
- **EFA** fabric exposed via the Nitro Card for VPC.
- **Neuron SDK** is the Trainium/Inferentia software stack (separate from NVIDIA).

### Azure ND-H100-v5

- 8× H100 80GB per VM
- NVLink 4.0 intra-VM
- **400 Gb/s Quantum-2 CX7 InfiniBand per GPU** (3.2 Tbps/VM)
- GPU Direct RDMA
- Sapphire Rapids CPU (96 cores), 1900 GiB RAM
- **Live Migration: NOT supported. Nested Virtualization: NOT supported.**
- This confirms GPU pass-through (dedicated GPUs per VM, no intra-VM sharing).
- IB auto-configures across VM Scale Set members.

### GCP A3 (High/Edge/Mega)

- Public material indicates GPU pass-through to H100 VMs with Google's **Titanium** offload (IPU/DPU-class) for storage/networking.
- **GVNIC** + **NetIRC** for RDMA over Ethernet.
- A3 Mega scales to 26k GPUs.
- Confidence is lower than AWS/Azure — docs URLs blocked in research.

## Run:ai (NVIDIA-owned) Fractional GPU — Recap

Covered in detail in [02-gpu-sharing.md](02-gpu-sharing.md). Summary:

- Splits **GPU memory** into enforced chunks (each pod gets its own virtual memory address space).
- Compute is shared via NVIDIA time-slicing by default.
- Creates "reservation pods" representing whole-GPU ownership to avoid collisions with other schedulers.
- Gang scheduler (all-or-nothing) for distributed workloads.
- NUMA-aware scheduling.
- Available self-hosted or SaaS, ships inside NVIDIA AI Enterprise.
- The product remains proprietary post-acquisition. Three Run:ai-adjacent components have been separately open-sourced under the `ai-dynamo` org: KAI Scheduler, Grove, Model Streamer.

## JuiceLabs and Emerging GPU Virtualization Software

- **JuiceLabs** — GPU virtualization fabric software. Markets position it as "GPU as fabric." Primary technical material was inaccessible in research (`juicelabs.io` returned 403). Treat with LOW confidence pending verification.
- **Project HAMi** (formerly Volcano-vgpu) — Chinese OSS GPU virtualization, alternative to Run:ai for non-NVIDIA-pure stacks.
- **Oroquesta / orc8r** — mentioned in some market maps; UNVERIFIED in this research pass.

## Emerging Architectures

- **BlueField DPU as tenant boundary** (CoreWeave's production model; NVIDIA productizing via DPF Operator).
- **Confidential GPU** (Hopper CC, Blackwell extending). Disables MIG, but enables the strongest software-isolation primitive.
- **GH200 / GB200 unified memory** — partitioning story for unified CPU+GPU memory is still settling. Current deployments favor whole-rack tenancy.
- **Disaggregated GPU pools** (Mooncake-style) — software pattern, not hardware partitioning primitive.

## The Stalwart

The Stalwart of this layer is **Cog** (Replicate's open-source model packager). A clean abstraction — "package a model into a Docker container with an OpenAPI schema" — that has become the conceptual model for "model as a deployable artifact." The Rust/Axum HTTP server inside Cog is the kind of focused engineering that distinguishes a Stalwart.

---

*Next: [08-sovereign-deployments](08-sovereign-deployments.md)*
