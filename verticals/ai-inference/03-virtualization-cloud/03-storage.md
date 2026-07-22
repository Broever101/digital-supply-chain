# Storage for AI Inference Workloads

## What Goes In

Model weights (10–700 GB single files). Datasets (TB-scale). KV cache (grows unboundedly during inference). Training checkpoints (TB-scale, written periodically).

## What Comes Out

Fast, durable, multi-tenant storage that lets GPUs spend their time computing instead of waiting for bytes.

Storage is the silent killer of inference performance. A model that takes 90 seconds to cold-start can ruin an autoscaling strategy. A KV cache that does not fit on the GPU forces either smaller context or slower decoding. A training checkpoint that takes 30 minutes to write stalls the training loop.

## The Three Storage Problems

Inference workloads have three distinct storage problems, and they need different solutions.

### Problem 1: Model weight loading (cold-start)

A 70B model in FP16 is roughly 140 GB. A 405B model is 800 GB. Loading that from disk to GPU memory at SSD speeds (3 GB/s) takes 45–270 seconds — an eternity for autoscaling. The fix:

- **Pre-tensorize weights** into a streaming format. CoreWeave's open-source **Tensorizer** (MIT, 318 stars, `github.com/coreweave/tensorizer`) streams from S3/HTTP/Redis at ~5 GB/s wire-speed (40GbE-bottlenecked). Pre-tensorized models are hosted free on CoreWeave's `s3://tensorized/` bucket. Encryption via libsodium.
- **ModelExpress** (part of NVIDIA Dynamo, `github.com/ai-dynamo/modelexpress`) streams weights GPU-to-GPU via NIXL/NVLink for **7× faster cold-start** (verified on DeepSeek-V3 on H200).
- **Run:ai Model Streamer** — parallel tensor-loading SDK from the (now NVIDIA-owned) Run:ai stack.

### Problem 2: KV cache tiering

The KV cache for a long-context workload can dwarf the model. The disaggregated prefill/decode pattern (covered in [orchestration](../04-orchestration/05-mooncake.md)) treats KV cache as a first-class object that flows between GPUs. Storage tiering for KV cache:

- **GPU HBM** — fastest, smallest (~80–192 GB per GPU).
- **CPU DRAM** — 10× larger, ~10× slower. Mooncake's pool layer.
- **Local SSD (NVMe)** — 100× larger, ~100× slower. Dynamo's KVBM layer.
- **Shared network storage (S3/Azure Blob)** — petabyte-scale, slowest. Dynamo 1.0 added S3 tier.

The right answer is **layered eviction** — hot KV blocks stay in HBM, warm ones in DRAM, cold ones on SSD, archival on object storage. All of Dynamo, Mooncake, and llm-d implement this pattern.

### Problem 3: Training checkpoints

Outside the scope of inference per se, but most sovereign cloud operators also train. The problem: a 405B model checkpoint is multiple terabytes, written every 30 minutes during training, blocking the training loop for minutes at a time. Solutions: GPFS, Lustre, WekaIO parallel writes.

## High-Performance Distributed Filesystems

The vendors that AI datacenter operators actually buy from:

- **WekaIO (WekaFS)** — software-defined parallel FS, runs on any NVMe node, ~80 GB/s per cluster. Strong AI workloads story.
- **VAST Data** — disaggregated shared-everything architecture, expensive but excellent at scale. Often paired with NVIDIA SuperPODs.
- **Pure Storage FlashBlade** — purpose-built NAS for AI, ~70 GB/s per blade.
- **IBM Storage Scale** (formerly Spectrum Scale, GPFS) — legacy HPC but still the standard at national labs.
- **Dell PowerScale** (formerly Isilon) — enterprise NAS, broad adoption.
- **NetApp ONTAP** — hybrid cloud story, enterprise standard.
- **Qumulo** — file-first, scale-out, Gartner-favorite.

## Object Stores

For model weights and datasets, S3-compatible object stores dominate:

- **MinIO** — open source, S3-compatible, default for on-prem.
- **Ceph** with RGW — open source, more general-purpose.
- **Scality** and **Cloudian** — enterprise S3-compatible.
- AWS S3, Google Cloud Storage, Azure Blob — the hyperscaler defaults.

## Container-Native Storage

For Kubernetes-native deployments:

- **Longhorn** (Rancher) — lightweight block storage.
- **Robin.io** — app-aware storage.
- **Mayastor** — high-performance NVMe over TCP.
- **OpenEBS** — umbrella project for multiple storage engines.

## Cache and Locality Layers

- **Alluxio** — data orchestration layer, caches hot data near compute.
- **Fluid** (Alibaba) — Kubernetes-native dataset acceleration.
- **Cascade storage tiers** — manual HBM → DRAM → SSD → object choreography.

## NVIDIA's Storage Stack

- **NVIDIA Magnum IO** — storage optimization stack, a collection of libraries and best practices.
- **GPUDirect Storage** — bypasses the host CPU, lets GPUs read directly from network-attached NVMe. Critical for fast cold-starts.
- **Dell + NVIDIA PowerScale integration with NIXL** — Dell claims 19× faster TTFT for inference workloads (November 2025 press release).

## The Reference Stack

For a sovereign cloud deploying inference today:

- **Model weights**: pre-tensorized (Tensorizer format) on S3-compatible object storage (MinIO or Ceph), with GPUDirect Storage where supported.
- **KV cache**: GPU HBM → CPU DRAM (via Dynamo KVBM or Mooncake Store) → local NVMe (for warmth) → object storage (for archival).
- **Datasets**: NFS or WekaIO for hot access, S3 for cold.
- **Training checkpoints**: GPFS or WekaIO parallel writes.

## The Stalwart

**Tensorizer** is the Stalwart of this layer. A small open-source project from CoreWeave (318 stars) that solves one problem — streaming model weights from S3 into GPU memory at wire speed — exceptionally well. With KNative, it makes serverless-scale-up of large models practical. The fact that CoreWeave open-sourced it instead of keeping it as a competitive moat is the Stalwart move.

---

*Next: [04-network-fabric](04-network-fabric.md)*
