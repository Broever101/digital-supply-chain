# Huawei Ascend — The Only Serious Chinese AI Silicon Stack

## What Goes In

SMIC fab capacity. TSMC-legacy equipment for older nodes. Chinese government subsidies. A national industrial policy with 70% semiconductor self-sufficiency target by 2028.

## What Comes Out

The only Chinese AI accelerator with credible software maturity, real production deployments, and a credible roadmap.

Huawei Ascend is to Chinese AI infrastructure what NVIDIA is to the US — the default. Every assumption about Chinese sovereign AI passes through Ascend.

## The Da Vinci Architecture

Ascend's compute architecture, fundamentally different from NVIDIA's tensor-core approach:

Each **Da Vinci "Max" AI Core** consists of:

- **3D Cube Tensor engine** — 4,096 FP16 MACs + 8,192 INT8 MACs per cycle. The matmul unit.
- **Vector unit** — 2,048-bit wide. Handles activation functions, normalizations.
- **Scalar unit** — control flow.

This is structurally different from NVIDIA's tensor cores. Da Vinci is denser on INT8, weaker on FP64, and designed for inference and transformer training rather than HPC simulation. The architecture choice reflects Huawei's priority: inference and consumer AI (where INT8 dominates) over scientific computing.

## The Chip Lineup

| Chip | Process | Cores | Performance | Memory | TDP | Status |
|---|---|---|---|---|---|---|
| **Ascend 310** (Mini) | TSMC 12nm | 2 Da Vinci | 16 TOPS INT8 / 8 TFLOPS FP16 | 8 MB on-chip | 8W | Inference, in production since 2018 |
| **Ascend 910** (Max, original) | TSMC 7nm N7+ | 32 Da Vinci (4 clusters) | 256 TFLOPS FP16 / 512 TOPS INT8 | 4× HBM2E, 1.2 TB/s, 32 MB cache | 350W | Original flagship; pre-sanctions |
| **Ascend 910A** | TSMC 7nm | — | — | — | — | Earliest 910 variant; superseded |
| **Ascend 910B** | **SMIC 7nm N+1** | 25 Da Vinci | Lower than 910 due to fewer cores | — | — | Main shipper 2023-2024; very different from 910 despite name |
| **Ascend 910C** | **SMIC 7nm N+2** (2nd-gen) | 2× 910B chiplets | **~60% of H100 inference** (per DeepSeek researchers) | — | — | **Mass-shipped May 2025**; ~H100-class |
| **Ascend 910D** | SMIC (process UNVERIFIED) | — | "Tests underway" | — | — | Samples delivered May 2025 per WSJ; rumored H200-class — UNVERIFIED |
| **Ascend 920** | **SMIC 6nm** | — | **~900 TFLOPS/card** | **HBM3, 4 TB/s** | — | Announced Apr 2025 as **H20 replacement**; mass production late 2025 |

The 910B → 910C jump matters because 910C is the first Chinese chip to reach rough H100 parity. The 910D, if it ships as rumored, would close the gap further. The 920 is positioned as the H20-class replacement for the China market.

## HCCS — Huawei's NVLink Equivalent

**HCCS (Huawei Cache Coherent Switch)** is the interconnect. Per Ascend 910:

- 3× 240 Gbit/s HCCS ports
- Plus 2× 100G RoCE for scale-out networking

HCCS aggregate bandwidth is roughly 3.3 Tbps — materially below NVLink 4's 4.8 Tbps and NVLink 5's 7.2 Tbps. This is the structural reason Huawei's CloudMatrix 384 needs so much optical networking to match NVIDIA's NVL72.

## CloudMatrix 384 — The Rack Answer to NVL72

The flagship Huawei rack-scale system:

- **384 Ascend 910C chips** across 16 racks (12 compute + 4 networking).
- **6,972 × 800G LPO optical transceivers** — entirely optical intra- and inter-rack.
- Performance beats NVIDIA GB200 NVL72 in absolute terms.
- **2.3× worse perf-per-watt** than NVL72.
- Price: RMB 60M (~$8.2M) per set.
- First deliveries late April 2025.

The trade-off is significant: CloudMatrix 384 wins on absolute throughput but loses on efficiency. For sovereign customers who care more about capability than opex, this is acceptable. For commercial customers competing on cost-per-token, it is a problem.

## CANN — The CUDA Equivalent

**CANN (Compute Architecture for Neural Networks)** is Huawei's compute stack. Includes:

- Operator libraries
- Compiler
- Runtime

Required dependency for everything above (vllm-ascend requires CANN 9.0.1 specifically).

**Critical**: CANN is **closed-source**, unlike CUDA which has substantial open components. This is the single biggest reason Ascend adoption outside China is hard. Every non-Chinese developer who tries Ascend hits the closed-toolchain wall.

## MindSpore — Huawei's PyTorch Alternative

- Released March 31, 2020. Latest 2.3.RC1 (April 24, 2024).
- Apache-2.0 license.
- **Source repo is on Gitee, not GitHub**: `gitee.com/mindspore/mindspore`. This is a structural finding, not a typo — the Chinese AI ecosystem runs on Chinese forges when it can.
- Cross-platform: Linux, Windows, macOS, EulerOS, openEuler, OpenHarmony, HarmonyOS, Android.
- Used for Huawei PanGu-Σ LLM.

**Realistic assessment**: MindSpore is losing the framework war to PyTorch globally, but remains the native path for PanGu and some Huawei-internal workloads. The strategic question is whether Huawei continues investing in MindSpore or fully embraces PyTorch via torch_npu.

## torch_npu — The PyTorch Path

`github.com/Ascend/pytorch` — mirror of `gitcode.com/Ascend/pytorch` (Chinese primary).

- 552 stars, 81 forks, 8,131 commits, 230 tags.
- Latest: **TorchNPU 26.0.0** (April 30, 2026) — supports PyTorch 2.10.0.
- Uses PyTorch's `PrivateUse1` dispatch key (the official extension mechanism for non-NVIDIA backends).
- Features: FSDP2 distributed training, DTensor, NPUGraph (graph-mode acceleration), AllGather/AllReduce/AllToAll collectives.

**This is the foundational layer** — every other framework on Ascend (vLLM-Ascend, MindIE, PaddlePaddle-Ascend) sits on top of torch_npu. The fact that it exists and is actively maintained is the single most important enabler of the open-source-on-Ascend ecosystem.

## MindIE — Huawei's TRT-LLM Equivalent

**MindIE (Mind Inference Engine)** is Huawei's production inference server for Ascend, equivalent in role to TensorRT-LLM. LOW-MED confidence based on prior knowledge; primary verification needed.

## Pangu, DeepSeek on Ascend, and the Production Reality

Real production usage of Ascend:

- **DeepSeek R1** trained on H800, runs **inference on Ascend 910C** (Wikipedia DeepSeek entry).
- **DeepSeek V4** (April 24, 2026) "built on Chinese chips" — Huawei and Cambricon (Quartz, MIT Tech Review).
- **Huawei expected to ship >800,000 Ascend 910B + 910C units in 2025** (WSJ via Wikipedia).
- **Ren Zhengfei** (Huawei founder) told Xi in February 2025 that "concerns about lack of chips have eased" and that a network of 2,000+ Chinese companies is targeting 70% semiconductor self-sufficiency by 2028.
- Customers publicly on Ascend: Tencent (vLLM Beijing meetup August 2025), DeepSeek, Baidu (PaddlePaddle native), iFlytek, Meituan, ByteDance (selective).

## The Economics

Why Ascend matters for cost-sensitive sovereign buyers:

- **BOM cost** of an Ascend 910C card is roughly 30–50% lower than an H100, depending on volume.
- **No export control premium** for buyers in Tier 2/3 countries.
- **No NVIDIA AI Enterprise license** cost.
- **Power consumption** is higher (the 2.3× perf-per-watt gap), but for sovereign buyers who care more about capability than opex, this is acceptable.
- **Software development cost** is higher — the CANN stack is less mature, kernel ecosystem is smaller.

## The Strategic Position

Huawei Ascend is the only Chinese AI silicon that:

1. Has a real software stack (CANN, torch_npu, MindIE, vllm-ascend, SGLang support).
2. Has real production deployments (DeepSeek, Tencent, Baidu, ByteDance, iFlytek).
3. Has a real roadmap (910D, 920, future generations).
4. Has real volume (800k+ units in 2025).

The other Chinese vendors (Cambricon, Biren, Moore Threads, Iluvatar, Hygon, MetaX — covered in [02-other-vendors.md](02-other-vendors.md)) are real companies with real products, but none has the same combination of stack + deployments + roadmap + volume. For a PK/GCC founder deciding which Chinese stack to bet on, Ascend is the answer.

## The Stalwart

The Stalwart of this layer is **torch_npu** — the open-source PyTorch backend for Ascend. Without it, the entire open-source-on-Ascend ecosystem would not exist. A small team at Huawei has quietly built and maintained the bridge that lets every PyTorch-based framework run on Chinese silicon. The fact that they did this through PyTorch's official `PrivateUse1` extension mechanism — rather than forking PyTorch — is the strategically important choice. It means the open-source world's investment in PyTorch now also benefits Chinese hardware.

---

*Next: [02-other-vendors](02-other-vendors.md)*
