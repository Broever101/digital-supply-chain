# Layer 3: Virtualization and Cloud

## How to Read This Chapter

This layer is structured as a **decision tree for building an AI datacenter from scratch** — specifically, a sovereign cloud. The use case is not "what GPU instance do I rent from AWS." It is "I am a country, a large enterprise, or a GCC sovereign wealth fund, and I need to operate my own AI infrastructure for data residency, national security, regulatory, or strategic-autonomy reasons. What do I need to think about?"

Most existing coverage of this layer conflates the **business** (how providers price and package compute) with the **tech** (how providers actually slice and isolate GPUs). This folder separates them. Business models live in the [end-user lens](../01-end-user-lens.md). Tech lives here.

## The Sovereign Cloud Decision Tree

If you are building a sovereign AI datacenter from scratch, you will make decisions in roughly this order:

1. **Where to build** — site selection, power, cooling. The foundations.
2. **What silicon to buy** — NVIDIA, AMD, Huawei Ascend, Cerebras, or mix. This decision is now politically constrained (see [Chinese hardware chapter](../07-chinese-hardware/)).
3. **How to architect the racks and network** — HGX vs DGX vs MGX vs DSX vs NVL72, fabric choice.
4. **How to provision bare metal** — PXE boot, MAAS, Tinkerbell, OS imaging, driver stack.
5. **How to slice GPUs** — MIG, vGPU, time-slicing, Run:ai, fractional GPU, or whole-GPU tenancy.
6. **How to design storage** — model weight loading, KV cache tiering, training checkpoints.
7. **How to architect the network fabric** — InfiniBand vs RoCE vs UEC, topology, SmartNICs.
8. **How to isolate tenants** — IAM, secrets, multi-tenancy primitives, confidential computing.
9. **How to observe and bill** — metrics, logging, tracing, cost attribution.
10. **What reference architecture to copy** — NVIDIA AI Enterprise, Red Hat OpenShift AI, SambaNova sovereign cluster, or roll your own from open source.

Each of those decisions has its own file in this folder.

## Files in This Layer

| File | What it covers |
|---|---|
| [01-bare-metal-provisioning.md](01-bare-metal-provisioning.md) | Site selection, power/cooling, racks (HGX/DGX/MGX/DSX/NVL72), OEM/ODM options, bare-metal provisioning (MAAS, Tinkerbell), OS choices (DGX OS, Ubuntu, Talos), driver/CUDA stack. |
| [02-gpu-sharing.md](02-gpu-sharing.md) | The virtualization primitives: NVIDIA MIG, MPS, vGPU, time-slicing; AMD SPX/DPX/QPX/CPX + NPS; Huawei vNPU; Run:ai fractional GPU; BlueField DPU as tenant boundary. When to use which. |
| [03-storage.md](03-storage.md) | High-performance distributed FS (WekaIO, VAST, Pure), object stores, KV cache tiering, GPUDirect Storage, NVIDIA Magnum IO. |
| [04-network-fabric.md](04-network-fabric.md) | InfiniBand (NDR/XDR), RoCEv2, Ultra Ethernet Consortium, NVLink/NVSwitch, topologies (fat-tree, rail-optimized, dragonfly), SmartNICs/DPUs (BlueField, Pensando), fabric management (UFM, CloudVision). |
| [05-iam-security.md](05-iam-security.md) | Vault, Keycloak, K8s RBAC, Cilium, Confidential Computing (Hopper CC, SEV-SNP, TDX), attestation, SBOMs, supply chain integrity, air-gapped operations. |
| [06-observability-billing.md](06-observability-billing.md) | Prometheus/VictoriaMetrics, Grafana, Loki, NVIDIA DCGM, Kubecost/OpenCost, billing primitives for AI workloads. |
| [07-provider-profiles.md](07-provider-profiles.md) | How specific providers actually virtualize — CoreWeave (BlueField + EVPN VXLAN), Lambda (Managed Slurm), Modal, Baseten (Truss + Dynamo), Replicate (Cog), Beam (beta9), RunPod/Vast.ai, AWS Nitro, Azure ND, GCP A3. Technical, not business. |
| [08-sovereign-deployments.md](08-sovereign-deployments.md) | Real-world sovereign case studies (G42, MBZUAI, Saudi+Groq, Argyll, Infercom, OVHcloud, SouthernCrossAI, JPMorgan) and reference architectures (DSX, AI Enterprise, OpenShift AI, Dell AI Factory, SambaNova sovereign). |

## The Single Most Important Strategic Shift

**The DPU is becoming the tenant boundary, not the hypervisor or the GPU.**

For ten years, the cloud industry assumed multi-tenancy meant "carve up a VM." CoreWeave's production architecture — every server runs an NVIDIA BlueField DPU under DOCA, per-customer virtual networks carved at line rate via EVPN VXLAN, GPUs allocated whole (no MIG, no slicing) — shows a different model. The DPU enforces isolation at the network layer; the GPU is dedicated. Noisy-neighbor problems disappear because the trust boundary moved.

This pattern is now being productized for the broader market as NVIDIA's **DPF Operator** (DPU Platform Framework) inside AI Enterprise. Expect it to become the default sovereign-cloud isolation primitive within 18 months.

## The Sovereign Cloud Stalwart Opportunity

The single biggest gap in the open-source ecosystem: **there is no widely-adopted open-source reference distribution for a sovereign AI datacenter.** NVIDIA's DSX platform (2026) is filling this on the commercial side; the OSS equivalent does not yet exist. The components all exist — Talos Linux, Cilium, MAAS, KAI Scheduler, DCGM, VictoriaMetrics, Vault, Harbor — but nobody has assembled them into a turnkey ISO.

Whoever builds this becomes the "Ubuntu of sovereign AI." The window is open through roughly 2027.

## Three Reference Architectatures at a Glance

| Stack | Vendor | What you get | Lock-in |
|---|---|---|---|
| **NVIDIA DSX / AI Enterprise** | NVIDIA + OEMs (Dell, HPE, SuperMicro) | Full AI factory: facility design + DGX OS + Run:ai + Dynamo + NIM + NeMo + support | High — entire stack is NVIDIA |
| **Red Hat OpenShift AI** | IBM/Red Hat | OpenShift + ODH + RHODS + multi-vendor hardware | Medium — OpenShift lock-in but multi-vendor |
| **Roll-your-own OSS** | You | Talos Linux + MAAS + Cilium + KAI Scheduler + Dynamo + vLLM + VictoriaMetrics + Vault + Harbor | Low — but you own the integration burden |

---

*Next layer: [04-orchestration](../04-orchestration/README.md)*
