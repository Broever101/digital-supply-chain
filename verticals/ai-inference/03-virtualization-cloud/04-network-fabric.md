# Network Fabric — The Hidden Layer

## What Goes In

Servers. GPUs. A physical building with cable trays.

## What Comes Out

A low-latency, high-bandwidth network that lets distributed inference workloads behave as a single computer.

The network fabric is the most under-discussed layer of the AI inference stack. People argue about GPUs and engines. They forget that an NVL72 rack has 130 TB/s of internal bandwidth and needs to be connected to other rackss at 800 Gb/s — and that the topology choices made here determine what workloads are even possible.

## Three Layers of Network

There are conceptually three networks inside an AI datacenter. They have different requirements and use different technology.

### Layer 1: Intra-rack — NVLink and friends

Inside a single 8-GPU HGX server or 72-GPU NVL72 rack, the GPUs talk to each other over **NVLink**. This is NVIDIA's proprietary fabric and is not negotiable for NVIDIA hardware.

- **NVLink 4.0** (Hopper): 900 GB/s per GPU
- **NVLink 5.0** (Blackwell): 1,800 GB/s (1.8 TB/s)
- **NVSwitch** — 64-port switch, 7,200 GB/s aggregate, with **SHARP** in-switch compute for collective operations.
- **NVLink-C2C** — chip-to-chip (Grace↔Hopper), 900 GB/s coherent.

For AMD, the equivalent is **Infinity Fabric**. For Huawei Ascend, it is **HCCS** (Huawei Cache Coherent Switch) — 3× 240 Gbit/s ports per Ascend 910, plus 2× 100G RoCE for scale-out. HCCS bandwidth is materially below NVLink (~3.3 Tbps aggregate vs NVLink 4's 4.8 Tbps / NVLink 5's 7.2 Tbps), which is why Huawei's CloudMatrix 384 needs 16 racks of mostly optical networking to match NVIDIA's NVL72.

### Layer 2: Inter-rack — InfiniBand or Ethernet

Between racks, the choice is InfiniBand vs Ethernet. This is one of the most consequential architectural decisions for an AI datacenter.

- **InfiniBand** — NVIDIA/Mellanox-owned. **NDR** at 400 Gb/s, **XDR** at 800 Gb/s. The fabric of choice for HGX clusters. Lower latency (~500 ns switch-to-switch), lossless by design, simpler protocols. NVIDIA **Quantum-2** (InfiniBand) is the current flagship switch; Quantum-X800 is the next gen.
- **RoCEv2** (RDMA over Converged Ethernet) — RDMA semantics over standard Ethernet. Cheaper than InfiniBand, more flexible, but requires careful tuning to be lossless.
- **Ethernet** (general) — what the rest of the datacenter runs on. Historically not suitable for AI scale-out because TCP is too slow.

### Layer 3: Management and tenant — Standard Ethernet

The customer-facing network and the cluster management network run on standard Ethernet. This is where the BlueField DPU and EVPN VXLAN patterns (covered in [GPU sharing](02-gpu-sharing.md)) operate.

## Ultra Ethernet Consortium (UEC) — The Alternative

Founded July 2023, the **Ultra Ethernet Consortium** is the industry's effort to build an Ethernet-based competitor to InfiniBand for AI scale-out. Steering committee: AMD, Arista, Broadcom, Bull/Atos, Cisco, HPE, Intel, Meta, Microsoft, Oracle. **NVIDIA joined August 2024** — a strategic hedge.

- Spec **v1.0.3** released July 2026 under Linux Foundation JDF.
- First products shipping 2024–2026 from Broadcom, Ultrascale, and others.
- The thesis: Ethernet is good enough if you fix the losslessness and routing problems.

Why this matters: UEC is the only credible threat to NVIDIA's InfiniBand monopoly. If UEC succeeds, AI fabric becomes multi-vendor. If it fails, NVIDIA owns the AI network stack the way it owns the GPU stack.

## NVIDIA Spectrum-X Ethernet

NVIDIA's own Ethernet-for-AI play, acknowledging UEC's existence. Pitched as InfiniBand-like lossless Ethernet. Bundled with BlueField DPUs as a turnkey "Spectrum-X Ethernet" fabric. Some large deployments (CoreWeave uses it on certain HGX builds).

## Topology Choices

The physical layout of switches and cables:

- **Fat-tree** — classic Clos topology, scales predictably, the default. Most production AI clusters.
- **Rail-optimized** — GPUs grouped by switch tier to minimize cross-rack traffic for collective ops. Standard for big InfiniBand clusters.
- **Dragonfly** — fewer switches, longer cables, used in HPC. Less common for AI.
- **Full-mesh** — every node connected to every other. Expensive, used at small scale.

The Huawei CloudMatrix 384 topology is unusual: 384 Ascend 910C chips across 16 racks (12 compute + 4 networking) with 6,912 × 800G LPO optical transceivers — entirely optical intra- and inter-rack. Beats NVIDIA GB200 NVL72 in absolute performance, but at 2.3× worse perf-per-watt.

## Switch Vendors

- **NVIDIA** (Quantum InfiniBand, Spectrum Ethernet) — the only end-to-end AI fabric vendor.
- **Arista** — Ethernet specialist, strong with UEC.
- **Broadcom** — silicon vendor (Memory, Tomahawk, Jericho families) — supplies the chips many other switch vendors use.
- **Cisco** — enterprise networking, AI racks partnership with NVIDIA.
- **Juniper** (now HPE) — datacenter Ethernet.

## SmartNICs and DPUs

The smarter the network interface card, the less work the host CPU has to do.

- **NVIDIA BlueField-3 / BlueField-4** — the dominant DPU. BlueField-4 (October 2025) gives 6× compute and 800 Gb/s. Runs DOCA software framework. The DPU enforces tenant isolation in CoreWeave's production model.
- **AMD Pensando** — AMD's DPU line, acquired 2022.
- **Intel IPU** (Infrastructure Processing Unit) — Intel's entry.

The DPU matters for sovereign clouds because it is the cleanest place to enforce multi-tenant isolation without trusting the host OS.

## Fabric Management

- **NVIDIA UFM** (Unified Fabric Manager) — the de facto InfiniBand management tool. Proprietary. **No widely-adopted open-source equivalent exists** — this is a Stalwart opportunity.
- **Broadcom Fabric Vision** — alternative for Broadcom-based networks.
- **Arista CloudVision** — Ethernet fabric management.

## Why This Matters for Inference

Three reasons. First, **tensor parallelism** splits each layer's matmul across GPUs, requiring all-to-all low-latency communication per layer. A 70B+ model on PCIe (126 GB/s) is roughly 7× slower than on NVLink 4 (900 GB/s) and 14× slower than on NVLink 5 (1,800 GB/s). The fabric choice determines whether multi-GPU inference is fast or terrible.

Second, **disaggregated prefill/decode** (covered in [orchestration](../04-orchestration/05-mooncake.md)) requires moving KV cache between GPU pools over the fabric. Mooncake's Transfer Engine delivers 87 GB/s on 4×200 Gbps RoCE and 190 GB/s on 8×400 Gbps RoCE — 2.4–4.6× TCP throughput. Fabric bandwidth is the binding constraint on this architecture.

Third, **expert parallelism** for MoE models (DeepSeek, Kimi, Mixtral) requires high all-to-all bandwidth. The economics of serving these models depend on the fabric.

## The Stalwart

The Stalwart here is the **UEC spec itself** — a multi-vendor effort to break NVIDIA's fabric monopoly. If it works, the structural dynamics of the entire AI infrastructure market shift. If it fails, NVIDIA owns three layers (GPU, NVLink, InfiniBand) instead of two.

The other Stalwart is **Mooncake's Transfer Engine** — quietly becoming the universal KV-cache transport layer, integrated into vLLM, SGLang, TensorRT-LLM, LMDeploy, LMCache, and NIXL. The 87/190 GB/s numbers it achieves on commodity RoCE fabric are the most important data-point in the disaggregated-inference era.

---

*Next: [05-iam-security](05-iam-security.md)*
