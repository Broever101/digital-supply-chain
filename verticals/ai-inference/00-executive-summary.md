# AI Inference

## Why This Matters

Training a model is a one-time expense. Serving it is forever.

Every chat reply, every AI-coded line, every agent step, every image generation, every transcription, every red-teaming eval — every single one of them is a request that someone has to push through a model sitting on a GPU somewhere. That act, called **inference**, is now the largest and most strategically contested market in technology. OpenAI, Anthropic, Google, xAI, Meta, and a hundred startups behind them are not racing to invent new model architectures. They are racing to push tokens through silicon faster, cheaper, and more reliably than anyone else.

This vertical maps the digital supply chain that makes inference possible. It runs from the wafers of HBM3e memory in an NVIDIA Blackwell GPU, up through the Kubernetes scheduler that decides which request goes to which GPU, up to the API gateway that bills your credit card when ChatGPT answers your question. Five technical layers, plus one end-user-facing layer that explains what you are actually paying for when you buy "inference."

## The Supply Chain at a Glance

Think of it as a power grid, not a pipeline:

1. **Hardware** (Layer 2) — The silicon. GPUs from NVIDIA and AMD, custom ASICs from Google and AWS, accelerator startups (Groq, Cerebras, SambaNova), the memory that holds the model, the fabric that links chips, and the cooling that keeps them from melting. **Chinese alternatives are covered separately in Layer 7** because the US/Chinese silicon universes have diverged.
2. **Virtualization and Cloud** (Layer 3) — How a $30,000 GPU becomes a $2-per-hour billable unit. Framed as a sovereign-cloud decision tree: bare-metal provisioning, GPU sharing (MIG, vGPU, time-slicing, Run:ai, BlueField DPU), storage, network fabric, IAM/security, observability, and provider profiles. A separate file covers real-world sovereign deployments.
3. **Orchestration** (Layer 4) — Five patterns: Kubernetes-native (KServe, llm-d), Ray-native (Anyscale), serverless platforms (Modal, Baseten, Replicate), disaggregated custom (NVIDIA Dynamo, Mooncake), and vendor stacks (Together, Fireworks, DeepSeek). Plus the cluster schedulers that make multi-node work.
4. **Inference Engines** (Layer 5) — The software that actually loads a model and emits tokens: vLLM, SGLang, llama.cpp, TensorRT-LLM, and the dozen specialized engines behind them.
5. **Application Layer** (Layer 6) — The gateways, routers, billing systems, observability tools, and benchmarks that developers actually touch.
6. **Chinese Hardware** (Layer 7) — Huawei Ascend and the other Chinese AI silicon vendors, the open-source stack that runs on them (vLLM-Ascend, SGLang, torch_npu), and the founder opportunities for PK/GCC builders.

Above all of this sits the **end-user lens** (Layer 1) — the layer where business models (aggregators, speed specialists, hyperscaler gateways, sovereign deployments, edge, frontier APIs) and performance metrics (TTFT, tokens-per-second, KV cache economics) determine who wins.

## The Big Picture

The AI inference market in 2026 is defined by one structural fact: **the same open-weight model is now available from at least 19 competing providers.** Artificial Analysis, the de facto independent benchmarking service, tracks 19 distinct vendors serving the exact same `gpt-oss-120b` weights — competing on price, latency, and reliability, but selling the identical underlying product.

This is commoditization in real time, and it is forcing the value chain to reorganize. Three shifts matter most:

**Margin is migrating away from raw token sales.** When 19 providers sell the same model, the per-token price collapses toward the marginal cost of GPU electricity. So providers are racing in two directions — down into specialized silicon that nobody else has (Groq's LPU, Cerebras's wafer-scale chip, SambaNova's RDU), or up into compliance, sovereignty, and enterprise trust (AWS Bedrock, Azure AI Foundry, sovereign-AI partnerships).

**Speed specialists are reframing themselves for the agentic era.** Groq, Cerebras, and SambaNova started by promising "fast Llama." In 2026 they pitch agentic loops and reasoning models, where wall-clock per turn determines how many tool calls an agent can fit inside a useful latency budget. The argument: tokens-per-second is no longer a UX nicety, it is the new quality lever — because faster inference lets you run more reasoning inside the same waiting time.

**The control plane consolidated in 2026, and open source outlived the losers.** In six months, the standalone usage-billing category was acquired out of existence: Metronome went to Stripe, Orb to Adyen, m3ter to Salesforce. Langfuse, the leading LLM observability company, was acquired by ClickHouse. And yet the open-source repos behind these companies (Langfuse at 31,000 stars, Lago at 10,000 stars) are more active than ever. The pattern: commercial entity acquired, OSS project persists. Anyone building "Stripe-for-AI-billing" today is building a feature Stripe already owns.

## What You Will Learn Here

- Why **TTime To First Token** matters more than tokens-per-second for chat, and why the opposite is true for agents
- What **KV cache** actually is and why it dominates GPU memory at long context
- How **PagedAttention** (a 2023 paper from UC Berkeley) quietly doubled the world's inference throughput
- Why **NVIDIA's GB200 NVL72** rack is a $3 million liquid-cooled slab that rewrites datacenter economics
- How **Groq, Cerebras, and SambaNova** stay in business against the GPU monopoly
- Why **sovereign AI** is the dark-horse business model nobody in Silicon Valley talks about
- Where the next Stalwart might be hiding — the small teams and open-source projects punching above their weight in a market dominated by trillion-dollar companies

## A Note on Confidence

This vertical was assembled with the project's 70/30 rule: AI for synthesis, humans for verification. Specific numbers cite primary sources where possible (vendor pricing pages, arXiv papers, SEC filings, Wikipedia spec tables). Where a claim is vendor-reported but not independently verified (Cerebras's "2,500 tokens per second on Llama 4 Maverick," Fireworks's self-reported $1B ARR), it is flagged in the body text. Where funding amounts could not be primary-sourced, they are marked `TBA` in `players.json` rather than guessed.

The AI inference market moves faster than any market this atlas covers. Star counts, pricing, and funding rounds shift weekly. Treat this snapshot as July 2026.

---

*Continue to the layers: [01-end-user-lens](01-end-user-lens.md) → [02-hardware-layer](02-hardware-layer.md) → [03-virtualization-cloud](03-virtualization-cloud/README.md) → [04-orchestration](04-orchestration/README.md) → [05-inference-engines](05-inference-engines.md) → [06-application-layer](06-application-layer.md) → [07-chinese-hardware](07-chinese-hardware/README.md)*
