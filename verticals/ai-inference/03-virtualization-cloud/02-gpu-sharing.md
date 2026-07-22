# GPU Sharing — How a GPU Becomes a Billable Unit

## What Goes In

A physical GPU (or a rack of them).

## What Comes Out

A slice of GPU compute that can be billed, isolated, and shared between workloads or tenants.

This is the heart of the virtualization layer. Every cloud economics model — per-GPU-hour, per-token, fractional, serverless — depends on how finely you can slice a GPU without losing performance or isolation.

## The Six Primitives

There are six ways to share a GPU. Each has different isolation guarantees and operational characteristics.

| Primitive | Isolation layer | Granularity | Reconfig cost | Hardware-bound |
|---|---|---|---|---|
| **MIG** (NVIDIA) | GPU silicon | 1/7 SM slices, 1/8 mem slices | Static — requires idle GPU | Yes (Ampere+) |
| **MPS** (NVIDIA) | Driver logical partitioning | % of SMs (no hard partitioning) | Process launch | No |
| **vGPU** (NVIDIA + VMware/Broadcom) | Hypervisor + driver split | Variable frame-buffer carving | VM lifecycle | Hopper/Ampere |
| **Time-slicing** | Driver context-switching | Whole GPU, time-multiplexed | Per timeslice | No |
| **Fractional GPU** (Run:ai) | Pod-level memory carve + time-slice | Arbitrary memory %, shared compute | Pod scheduling | No (software) |
| **Whole-GPU + DPU boundary** (CoreWeave) | Network isolation at DPU | Whole GPU per tenant | DPU program | Yes (BlueField) |

There are also hardware-specific primitives for non-NVIDIA silicon:

- **AMD**: SPX/DPX/QPX/CPX compute partitioning + NPS memory partitioning + SR-IOV for hypervisor-level VF carving.
- **Huawei Ascend**: **vNPU** slice primitive, managed via the CANN stack and ClusterDirector. Documentation is gated; the matrix of supported slice sizes and the isolation boundary could not be primary-sourced.

## NVIDIA MIG — Hardware-Level Partitioning

MIG (Multi-Instance GPU) is the strongest isolation NVIDIA offers. Available on Ampere (A100, A30) and later datacenter GPUs: A100, A30, H100, H200, H20, GH200, B200, GB200, RTX PRO 6000 Blackwell. **Unavailable on consumer GeForce cards by NVIDIA policy** — an artificial market-segmentation move.

How it works:

- A GPU is sliced into **7 SM slices** (each ≈1/7 of the Streaming Multiprocessors) and **8 memory slices** (each 1/8 of memory + memory controllers + L2 cache).
- A **GPU Instance (GI)** combines N memory slices + M SM slices + dedicated engines (DMAs, NVDECs, JPEG, OFA).
- A **Compute Instance (CI)** further subdivides a GI's SM slices while sharing memory and engines.
- Maximum 7 instances per GPU (A30 capped at 4).
- **Hardware error isolation** between partitions, memory bandwidth QoS per slice, dedicated L2 cache partitions.

MIG reconfig requires the GPU be **idle**. This makes dynamic re-partitioning impractical for production — most operators pick a partitioning scheme at deploy time and leave it.

## NVIDIA MPS — Logical Partitioning

MPS (Multi-Process Service) is older, software-level, less isolated. Up to 48 clients share a GPU; each gets a percentage of SMs but no hard partitioning. Shared bandwidth and error domain. Useful for low-stakes multi-tenancy within a single team. Do not use across distrust boundaries.

## NVIDIA vGPU — Hypervisor-Level

vGPU (formerly GRID) is the hypervisor-level virtualization story, usually paired with VMware/Broadcom or Nutanix. The host runs a "Virtual GPU Manager" driver; the guest runs a vGPU Compute Guest Driver. Frame-buffer is carved variable-size. Mostly used for VDI and general workloads, less common for AI inference. License: requires **NVIDIA AI Enterprise** for the full stack.

## Time-Slicing — The Default

Software round-robin between contexts. Lowest overhead, lowest isolation. Default on most cloud "fractional GPU" offerings. Causes noisy-neighbor problems. Fine for batch jobs where latency doesn't matter; terrible for interactive workloads.

## Run:ai Fractional GPU

Acquired by NVIDIA in April 2024 for ~$700M. The headline primitive: **fractional GPU**.

How it actually works:

- **Memory is hard-partitioned.** Each pod gets its own virtual memory address space; one workload cannot exceed its allocation.
- **Compute is shared** via NVIDIA time-slicing (or Run:ai's own time-slicing implementation).
- Creates "reservation pods" representing whole-GPU ownership to avoid collisions with other schedulers.
- Known to cause GPU memory fragmentation.
- Pairs with a **gang scheduler** (all-or-nothing pod scheduling) and NUMA-aware placement.

Product: now **NVIDIA Run:ai**, available self-hosted or SaaS, ships inside NVIDIA AI Enterprise Infra 7.7 LTSB / 8.1.

**Important correction to common belief**: the broader open-source community expected NVIDIA to open-source Run:ai after the acquisition. The product remains proprietary. However, NVIDIA has separately open-sourced three components under the `ai-dynamo` GitHub org that overlap with Run:ai's function: **KAI Scheduler** (gang scheduling, topology-aware — originally Run:ai code), **Grove** (inference framework + K8s scheduling bridge, part of NVIDIA Dynamo), and **Run:ai Model Streamer** (parallel tensor-loading SDK for cold-start mitigation). The unified UI and multi-cluster wrapper remain closed.

## CoreWeave — Whole-GPU + DPU Boundary

The most interesting production model in the market. CoreWeave does **not** use MIG, vGPU, or fractional GPU. Every customer gets dedicated whole GPUs. Multi-tenancy isolation happens at the **NVIDIA BlueField DPU**, not at the GPU or hypervisor.

How it works:

- Every server runs a BlueField DPU under DOCA (NVIDIA's DPU software framework).
- Per-customer virtual networks are carved at line rate via **EVPN VXLAN**.
- The DPU enforces network and storage isolation; the GPU is dedicated to one tenant.
- "No noisy neighbors" rests on the DPU boundary, not GPU partitioning.

BlueField-4 (announced October 2025) gives 6× compute and 800 Gb/s. NVIDIA's **DPF Operator** (DPU Platform Framework) inside AI Enterprise is the productization of this pattern for the broader market. Expect it to become the default sovereign-cloud isolation primitive within 18 months.

## AMD MI300X — SPX/DPX/QPX/CPX + NPS

AMD does not have a MIG equivalent. Instead, two orthogonal partitioning axes:

**Compute partitioning** (XCC → XCP grouping). MI300X has 8 XCDs (=8 XCCs). Modes:
- **SPX** — 1 logical GPU (all 8 XCDs)
- **DPX** — 2 logical GPUs (4 XCDs each)
- **QPX** — 4 logical GPUs (2 XCDs each)
- **CPX** — 8 logical GPUs (1 XCC each)
- TPX exists but not on MI300X (8 does not divide by 3); MI300A (6 XCDs) supports TPX instead of QPX.

**Memory partitioning** (NPS = Partitions Per Memory):
- **NPS1** — all HBM interleaved
- **NPS2** — DPX-only
- **NPS4** — QPX/CPX-only
- **NPS8** — defined but unsupported on MI300X

**CPX + NPS4** gives ~1 TB/s per AID — best for bandwidth-bound parallel workloads.

**Operational limits**:
- Changing partitions requires root + idle GPU.
- **Memory changes require a driver reload** (`modprobe -r amdgpu && modprobe amdgpu`) that reconfigures every GPU in the hive.
- Partition ID is encoded in PCIe BDF bits.
- Each CPX partition appears as separate `/dev/dri/renderD<N>`.

**SR-IOV** is the actual virtualization story (separate "AMD SMI for Virtualization" docs). Host PF driver carves VFs with partitioning modes + frame-buffer sharing. SR-IOV guests can query but not set partitions — the hypervisor withholds host partition details for security.

## Huawei Ascend vNPU

The Ascend 910B/910C exposes a **vNPU** slice primitive analogous to MIG, managed via Huawei's CANN stack and ClusterDirector. The Huawei documentation portal (hiascend.com) is gated behind a login and primary technical material on the matrix of supported slice sizes and the isolation boundary is not openly accessible. Treat any specific claim as needing verification.

## When to Use Which — A Decision Matrix

| Workload | Best primitive | Why |
|---|---|---|
| Production inference for one customer | MIG 1-of-7 | Hardware isolation, predictable perf |
| Multi-tenant inference (many small workloads) | Run:ai fractional GPU | Software flexibility, dynamic allocation |
| Production inference, strict isolation, no shared GPU | Whole-GPU + DPU (CoreWeave model) | Network-level tenant boundary |
| Research batch jobs, same team | Time-slicing | Simplicity |
| AMD-based inference | CPX + NPS4 | Best bandwidth-bound partitioning |
| Sovereign cloud, regulated | Whole-GPU + Confidential Computing | Hardware attestation, no operator visibility |

## Confidential Computing — The Strongest Isolation

**NVIDIA Confidential Computing** on Hopper (and extending to Blackwell with Vera Rubin). Hardware root-of-trust + attestation. The tenant's code and model weights are protected even from the host operator — the GPU's HBM is encrypted at rest, decrypted only inside the GPU's trusted execution environment.

- **PPCIE mode** enables multi-GPU setups where NVLink carries plaintext within a verified trust domain.
- Works with AMD SEV-SNP and Intel TDX CPU confidential computing.
- **Adopters**: Google Cloud, Azure, Anthropic, xAI, Deepgram, ElevenLabs, Anjuna, Edgeless, Fortanix, Opaque, EQTY Lab, Red Hat.
- **Apple Private Cloud Compute** uses NVIDIA CC on Google Cloud for Apple Foundation Models — the canonical "corporate-sovereign without owning a datacenter" pattern.
- **3rd generation** CC extends across the whole NVL72 rack (72 GPUs + 36 Vera CPUs unified security domain).

**Important constraint**: CC **disables MIG** — you cannot combine CC isolation with MIG partitioning on the same GPU today. The two isolation primitives are mutually exclusive.

**Open source tools**: `nvtrust` (Apache-2.0, 318 stars, `github.com/NVIDIA/nvtrust`) provides guest-side attestation SDK and host tools to configure GPU CC mode, with sample KVM scripts for launching Confidential VMs.

## The Stalwart

The Stalwart of this layer is the **AMD MI300X's CPX mode**, not because it is elegant (it is not), but because it forced the industry to acknowledge that NVIDIA's MIG is not the only valid hardware partitioning primitive. The fact that an AMD partition requires a driver reload to reconfigure, while MIG does not, sounds like a weakness — but it is actually the source of AMD's stronger memory isolation between partitions. Different tradeoff, real engineering.

The other Stalwart is **Project HAMi** (formerly Volcano-vgpu), a Chinese OSS GPU virtualization project that is the alternative to Run:ai for non-NVIDIA-pure stacks. Quietly important for the Chinese-hardware ecosystem covered in [Layer 7](../07-chinese-hardware/README.md).

---

*Next: [03-storage](03-storage.md)*
