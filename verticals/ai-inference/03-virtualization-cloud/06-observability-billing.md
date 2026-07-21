# Observability and Billing

## What Goes In

A running cluster. Users consuming resources. A need to know what's happening, who's using what, and how to bill for it.

## What Comes Out

Dashboards. Alerts. Per-tenant cost reports. A bill at the end of the month.

Observability and billing are covered together because they share the same primitive: time-series metrics. The difference is whether the metrics drive alerts or invoices.

## Observability — The Standard Stack

- **Metrics**: **Prometheus** (the default), **VictoriaMetrics** (10× lower resource use than Prometheus + Thanos — increasingly the production choice), **Thanos / Cortex / Mimir** for long-term storage and federation.
- **Dashboards**: **Grafana** — the universal default.
- **Logging**: **Loki** (Grafana's Pairs), **ClickHouse** (for high-cardinality logs — increasingly popular), **Elasticsearch** (legacy).
- **Tracing**: **Tempo** (Grafana family), **Jaeger** (CNCF).
- **OpenTelemetry** — the open standard that ties it all together.

## GPU-Specific Observability

The metric sources that matter for AI workloads:

- **NVIDIA DCGM Exporter** — the canonical GPU metrics exporter. Exposes Prometheus metrics for GPU utilization, memory, temperature, power, ECC errors, etc. Required baseline.
- **AMD MI300 telemetry** — ROCm-SMI equivalent, less mature than DCGM.
- **Huawei Ascend** — NPU profiling via CANN tools. Less standardized; no first-class OpenTelemetry integration. **This is a real gap** — see [07-chinese-hardware/03-software-stack.md](../07-chinese-hardware/03-software-stack.md).
- **NVIDIA NSight Systems / NSight Compute** — for deep profiling at the kernel level. Not for production monitoring, but for performance debugging.
- **KCLOUDs** — Kubernetes-native GPU observability.

## Cost Attribution and FinOps

How do you know how much each workload cost?

- **Kubecost** — the leading Kubernetes cost-allocation tool. Open-source core, commercial tier.
- **OpenCost** — the CNCF sandbox project that Kubecost donates to. Allocated costs by namespace, label, workload.
- **CloudHealth** (VMware/Broadcom), **Vantage**, **Cloudability** — commercial multicloud FinOps.

## Billing Primitives for AI

The actual mechanics of billing for AI inference. This is more nuanced than billing for VMs because the unit economics are different.

- **Per-GPU-hour** — straightforward. Metered from pod start to pod stop. Works for dedicated deployments.
- **Per-token** — metered from API request/response. Requires a token-counting layer (tiktoken, HF tokenizers). Works for inference APIs.
- **Per-second (serverless)** — metered at sub-second granularity. Requires checkpoint/restore or fast container cold-starts. Modal's per-CPU-cycle billing is the extreme form.
- **Per-invocation** — metered per request, regardless of duration or size. Replicate's per-output-image billing is the example.

## Token Metering

For inference providers, the billing primitive that matters is **token metering**. Every request must be counted accurately, at line rate, without slowing down inference.

- **tiktoken** (OpenAI, 18,782 stars, MIT) — the de facto token counter for OpenAI models.
- **Hugging Face tokenizers** (10,896 stars, Apache-2.0) — Rust core, fast, multi-model.
- Custom metering proxies (LiteLLM, Helicone) typically use one of these under the hood.

For a sovereign cloud operator, the choice is: do you bill by GPU-hour (simple, but unfair to small workloads), by token (fair, but requires a metering layer), or both (segmented tiers — Standard, Priority, Flex)?

## The 2026 Billing Industry Consolidation

For context (covered in detail in [06-application-layer.md](../06-application-layer.md)):

- **Metronome** → Stripe (~$1B), Dec 2025
- **Orb** → Adyen ($335M), Jun 2026
- **m3ter** → Salesforce, 2026
- **Lago** (10,236 stars, AGPL-3.0) — the OSS survivor

For sovereign clouds, the implication: don't build a billing system; integrate Stripe or build on Lago.

## Reference Observability Stack

For a sovereign cloud deployment:

- **Metrics**: VictoriaMetrics (lower resource use than Prometheus+Thanos)
- **Dashboards**: Grafana
- **GPU metrics**: NVIDIA DCGM Exporter
- **Logs**: ClickHouse (high-cardinality) or Loki (simpler)
- **Traces**: Tempo + OpenTelemetry SDKs in inference engines
- **Cost**: Kubecost (or OpenCost for pure OSS)
- **Billing**: Stripe Metered Billing (commercial) or Lago (OSS)

## The Stalwart

**VictoriaMetrics** is the Stalwart of this layer. A small team built a Prometheus-compatible metrics database that uses 10× less memory and disk than Prometheus + Thanos, at comparable or better query performance. In a world where AI workloads generate enormous metric volumes (per-GPU, per-request, per-token), this matters. The fact that VictoriaMetrics is not yet the default everywhere is mostly inertia, not technology.

---

*Next: [07-provider-profiles](07-provider-profiles.md)*
