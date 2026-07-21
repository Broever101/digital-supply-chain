# Cluster Schedulers — Volcano, Kueue, YuniKorn, Run:ai

## What Goes In

A Kubernetes cluster. Workloads that need many GPUs atomically (gang scheduling) or that need careful topology-aware placement.

## What Comes Out

Distributed inference pods that actually schedule, rather than sitting forever at 6/8 GPUs.

Cluster scheduling is the unsexy prerequisite for serious multi-node inference. The vanilla Kubernetes scheduler cannot gang-schedule, which means it cannot reliably place a tensor-parallel inference pod that needs 8 GPUs at once. The four tools in this file solve that problem in different ways.

## Why Gang Scheduling Matters

A tensor-parallel 70B inference pod needs 8 GPUs simultaneously. Without gang scheduling:

- Kubernetes places 6 of 8 pods successfully.
- The 7th pod stays pending because no GPU capacity is free.
- The 6 placed pods sit forever, holding their GPUs, waiting for the 7th.
- No other workload can use those 6 GPUs.
- Result: stranded capacity, blocked queue.

Gang scheduling solves this: schedule all-or-nothing. Either all 8 pods go down together, or none do.

## The Four Options

### Volcano

- **CNCF** project, Huawei-led.
- Batch and gang-scheduling for Kubernetes.
- The most mature option for AI/ML workloads.
- Apache-2.0.

`volcano.sh`.

### Kueue

- **Kubernetes SIG-Scheduling** project.
- Default batch queuing in K8s 1.30+.
- The "boring standard" for batch workloads.
- Apache-2.0.

`kueue.sigs.k8s.io`.

### Apache YuniKorn

- **HPC-heritage** scheduler.
- YARN-Kubernetes hybrid scheduling.
- Useful for orgs with HPC background (national labs, research institutions).
- Apache-2.0.

`yunikorn.apache.org`.

### KAI Scheduler (Run:ai-derived, open-sourced)

- Originally Run:ai proprietary code.
- **Open-sourced by NVIDIA post-acquisition** under the `ai-dynamo` GitHub org.
- Gang scheduling, topology-aware placement.
- The scheduling component of NVIDIA Dynamo.

## Run:ai Itself — Recap

**NVIDIA Run:ai** (acquired April 2024 for ~$700M) is the productized, proprietary, NVIDIA-supported version. Ships inside NVIDIA AI Enterprise Infra 7.7 LTSB / 8.1. Available self-hosted or SaaS.

- **Fractional GPU** primitive (covered in [03-virtualization-cloud/02-gpu-sharing.md](../03-virtualization-cloud/02-gpu-sharing.md)).
- Gang scheduler.
- NUMA-aware scheduling.
- Multi-cluster management UI (proprietary, not open-sourced).

Important: **the broader Run:ai product remains proprietary post-acquisition.** Three Run:ai-adjacent components have been separately open-sourced under the `ai-dynamo` org:

- **KAI Scheduler** — the gang scheduler.
- **Grove** — topology-aware placement for NVL72.
- **Run:ai Model Streamer** — parallel tensor-loading SDK for cold-start mitigation.

The unified UI and multi-cluster wrapper remain closed.

## Topology-Aware Scheduling — The NVL72 Problem

The unit of scheduling has changed. For an HGX H100 8-GPU node, gang scheduling across 8 GPUs in one node is enough. For an NVL72 rack, the unit is **72 GPUs across a rack** with NVLink domain semantics.

This requires **topology-aware scheduling** — the scheduler must understand rack boundaries, NVLink topology, and cooling constraints. **Grove** (part of NVIDIA Dynamo) is purpose-built for this.

## The Utilization Problem

Idle GPUs are pure cost. Industry estimates of typical cluster utilization are **30-50% without smart scheduling** — the entire reason Run:ai exists. Hyperscalers achieve higher (60-75%) via massive workload diversity. Neo-clouds often run 40-55%.

This is the central economic fact of cluster management. A GPU sitting idle costs the same as a GPU running hot. Whoever can push utilization from 40% to 70% without violating latency SLAs captures the margin. This is why gang scheduling, prefix caching, disaggregated prefill/decode, and KV-aware routing matter so much — they are all utilization improvers.

## When to Use Which

| Workload | Right scheduler |
|---|---|
| Single-pod inference, no gang | Vanilla K8s scheduler |
| Multi-pod batch, no special topology | Kueue (default in K8s 1.30+) |
| AI/ML batch with gang requirements | Volcano |
| HPC-heritage org | Apache YuniKorn |
| NVL72 topology-aware | Grove (in Dynamo) |
| Full Run:ai product (fractional GPU + UI + multi-cluster) | NVIDIA Run:ai (commercial) |

## The Stalwart

**Kueue** is the Stalwart of this layer. A boring, standard, SIG-Scheduling project that became the default batch queuing mechanism in K8s 1.30+. Not flashy, not feature-rich, but the right answer for most workloads. The kind of infrastructure you forget exists, which is the highest praise.

---

*Next layer: [05-inference-engines](../05-inference-engines.md)*
