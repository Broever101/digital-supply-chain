# Layer 0: The End-User Lens — What You Are Actually Buying

## Why This Chapter Comes First

The five technical layers that follow — hardware, cloud, orchestration, engines, application — describe how inference is built. But to understand why those layers look the way they do, you first have to understand what the **buyer** of inference actually cares about.

If you are a developer picking an inference provider for, say, a coding copilot, your decision is not really about GPUs or Kubernetes. It is about six things: **latency, throughput, quality, cost, reliability, and compliance.** The entire supply chain exists to deliver those six things, and every business model in the market is a different bundle of tradeoffs between them.

This chapter maps the business models, the metrics, and the workloads they serve. It is the framing for everything that follows.

## What Goes In

A trained model. A stream of user requests. A credit card.

## What Comes Out

Tokens, billed by the million, with a developer experience wrapped around them.

## The Business Model Taxonomy

The AI inference market in 2026 splits into six categories. The boundaries blur — most serious players straddle two or three — but the economic primitives differ enough that the taxonomy holds.

### 1. Generic Aggregators / Routers

These companies do not run their own models in the traditional sense. They proxy your request to whichever underlying provider they think is best, add a small markup, and handle billing, failover, and observability on top.

- **OpenRouter** — The largest "model marketplace." Lists 400+ models from 70+ underlying providers, charges a 5.5% fee on pay-as-you-go usage. Valued at $1.3 billion as of May 2026. This is the default model dropdown for many indie AI apps.
- **Together AI** — Runs many open models on its own infrastructure but also functions as an aggregator for fine-tunes and dedicated deployments.
- **DeepInfra** — Low-cost per-token inference with a $107 million Series B behind it. Among the cheapest published rates in the market.
- **Fireworks AI** — Recently crossed $1 billion in self-reported ARR. Now also resold *inside* Azure AI Foundry — an unusual "aggregator-of-aggregators" inversion.
- **Replicate** — Per-second billing for community models, powered by its open-source **Cog** packager.

### 2. Speed Specialists (Custom Silicon)

These companies built their own chips to do inference faster than any GPU can. Their pitch used to be "fast Llama at cheap prices." In 2026 it has evolved into "fast inference is the new quality lever, because it lets you run more reasoning inside your latency budget."

- **Groq** — The **LPU** (Language Processing Unit) uses SRAM instead of HBM and a deterministic architecture. Claims 500 tokens/sec on Llama 3.3 70B and 1,000 tokens/sec on gpt-oss-20b. Revenue of $500 million in 2025. Signed a roughly $20 billion licensing deal with NVIDIA in December 2025.
- **Cerebras** — The **Wafer-Scale Engine 3** is a single chip the size of a dinner plate, with 900,000 cores and 4 trillion transistors. Beat Blackwell on Llama 4 Maverick benchmarks. Went public in May 2026 at a $95 billion valuation — the largest US tech IPO since 2019.
- **SambaNova** — The **RDU** (Reconfigurable Dataflow Unit) is the speed specialist pivoting hardest to sovereign AI. Raised the first close of a $1 billion round at an $11 billion valuation in July 2026.

### 3. Dedicated / Sovereign / Hyperscaler Gateways

These are the players enterprises trust with regulated data and large contracts. They are not the cheapest, but they offer compliance signatures, data residency, and SLAs that aggregators cannot.

- **AWS Bedrock** — Carries 18 model providers including direct competitors (OpenAI, Anthropic, Google, xAI). The most multi-vendor of the hyperscaler gateways.
- **Azure AI Foundry** (formerly Azure OpenAI) — Microsoft's unified AI platform. Serverless catalog includes Fireworks as a sub-provider. Offers Provisioned Throughput Units (PTUs) that can flex across OpenAI *and* DeepSeek models.
- **Google Vertex AI** — Direct access to Gemini models and Google TPUs.
- **Cohere Model Vault** — The only frontier lab with a dedicated single-tenant product purpose-built for enterprise retrieval (embed + rerank).

### 4. Self-Serve GPU Clouds and Serverless Platforms

These rent you raw GPUs by the hour (or second), and let you run whatever you want. Useful for custom models, fine-tuning, or escaping the lock-in of managed APIs.

- **CoreWeave** — The dominant "neo-cloud." Public company (Nasdaq: CRWV) since March 2025. First to deploy GB200 NVL72 in February 2025 and first to ship GB300 commercially in July 2025. Customers include Microsoft, OpenAI ($12 billion / 5-year deal), Meta ($21 billion deal), and Anthropic.
- **Lambda** (formerly Lambda Labs) — A favorite of ML researchers and indie developers. Hourly H100s at roughly half hyperscaler rates.
- **Crusoe Energy** — Powers its datacenters with flared natural gas from oil wells. Subsidized by otherwise-wasted energy.
- **Modal, Baseten, Replicate** — Serverless-per-second platforms. Modal bills per CPU cycle with no idle commitment; Baseten focuses on production deployments (its customer list — Cursor, Notion, Descript, Sourcegraph — is the who's who of AI-native apps).
- **RunPod, Vast.ai** — Spot-market GPU clouds. RunPod's "Community Cloud" tier sells RTX 4090 time at $0.34/hour — the lowest published GPU rate anywhere.

### 5. Edge and On-Device

Inference that happens on the user's phone, laptop, or desktop — not in a datacenter.

- **Ollama** — The de-facto local-LLM runtime. Open-source core (176,000 GitHub stars), cloud upsell at $20/month for Pro. The local product is the moat; the cloud is the revenue.
- **LM Studio** — Closed-source desktop GUI on top of llama.cpp. Recently launched "LM Studio Bionic," a pivot from "model runner" to "agent runtime."
- **Apple Intelligence** — The OEM-subsidized extreme. Apple's unified memory architecture means an M3 Ultra desktop can run Llama 3 70B entirely in RAM. Who pays for this? The device buyer, amortized over the lifetime of the machine.

### 6. Frontier-Lab Proprietary APIs

The labs themselves sell inference directly, at premium prices, for their proprietary frontier models.

- **Anthropic** — Pricing ranges from $1/$5 per million tokens (Haiku 4.5) up to $10/$50 (Fable 5). The most aggressive prompt-caching economics in the frontier tier, with separate 5-minute and 1-hour cache TTLs.
- **OpenAI** — The GPT-5.6 family spans $1.10/$6.60 (Luna) to $5.50/$33.00 (Sol). Notably also offers **gpt-oss** open-weight models at parity with Groq — a frontier lab voluntarily commoditizing its own low end.
- **xAI** — Grok 4.5 pricing includes a long-context surcharge: $2/$6 below 200k context, $4/$12 above. Voice realtime at $0.05/minute.
- **Google Gemini, Cohere, Mistral, AI21** — Each with their own niche.

### Two patterns that cross all categories

First, **service-tier segmentation has become an industry standard.** AWS Bedrock, DeepInfra, and Anthropic all now offer roughly the same tiers: **Standard** (1×), **Priority** (+75% for latency-sensitive workloads), **Flex** (−50% for async batch), and **Reserved** (committed use). **Batch −50% is universal.** This is pure margin optimization that did not exist in the 2023-era "one price per model" world.

Second, **prompt caching is now the second-largest line item in frontier-model bills.** Whoever owns the cache-TTL strategy owns the unit economics of long-context agent workloads.

## The Metrics That Actually Matter

When you compare inference providers, six families of metrics determine who wins your workload.

### Latency

- **Time To First Token (TTFT)** — The delay between sending a request and seeing the first token stream back. Includes network, queueing, and **prefill** (the time the model spends reading your prompt). A 3-second TTFT feels broken even if decode is fast. Typical ranges: GPT-4o at ~0.77 seconds, frontier APIs at 1–3 seconds, dedicated open-model deployments on Groq or Cerebras at 0.1–0.4 seconds.
- **Time To First Answer Token** — A refinement for reasoning models. Claude's "thinking" or DeepSeek-R1's reasoning can produce 10–60 seconds of internal tokens before any user-facing answer appears. TTFT-to-first-byte is not the same as time-to-useful.
- **Inter-token latency / output speed** — How fast tokens stream after the first one. Reported as tokens/sec. Below 20 feels sluggish, above 50 feels instant. GPT-4o streams at ~186 tokens/sec.
- **Tail latency (P50/P90/P99)** — The 50th, 90th, and 99th percentile latencies across many requests. P50 flatters providers. P99 is what breaks UX under load. Agentic loops are especially tail-sensitive: one slow step stalls the whole chain.

### Throughput

- **Per-request tokens/sec** — Output speed for one request, as felt by the user.
- **Aggregate throughput** — Total tokens/sec across all concurrent requests on a GPU. Determines how many users one GPU can serve and what the provider's $/token floor is.
- **Tokens/sec/GPU, tokens/sec/$** — The metrics providers actually optimize. Continuous batching (covered in Layer 4) is the single biggest throughput unlock of the era — Anyscale's benchmark showed up to **23× improvement** over naive batching.

### Quality-impacting knobs

- **Quantization** — Compressing model weights (and sometimes activations) to lower bit-widths to cut memory and boost decode speed. **FP16/BF16** is the baseline. **FP8**, native on NVIDIA Hopper and Blackwell, matches FP16 quality at roughly 2× the throughput. **4-bit** methods like **AWQ** (activation-aware) and **GPTQ** are near-lossless for instruction-tuned models and give 3×+ speedups. Below 4 bits — formats like MXFP4 or NVFP4 — quality degrades non-linearly and is only worth it when memory is the hard constraint.
- **Speculative decoding** — A small "draft" model proposes tokens; the big model verifies them in one forward pass. Lossless in quality. **EAGLE-3** is the current state of the art, claiming 4–6× speedups on small and mid-size models. Trade-off: net positive when you are latency-bound (interactive, agentic), net negative when your GPUs are already saturated.
- **Context-length handling** — Sliding window attention (loses tokens outside the window), RoPE scaling and **YaRN** (extend context with fine-tuning), and Ring Attention (distributes long-context attention across GPUs for 1M+ token contexts).

### Memory and the KV cache

For every token in your prompt, the model stores Key and Value tensors so future tokens can attend to them. This **KV cache** dominates GPU memory at long context. A rough rule of thumb: ~1MB per token for a 13 billion-parameter model. At 128k context, that is 128 gigabytes of state per request — far more than the model itself.

The KV cache is the central constraint in LLM serving and the source of almost every optimization since 2023. The breakthroughs:

- **PagedAttention** (vLLM, SOSP 2023) — OS-style virtual paging of KV cache into fixed blocks, cutting memory waste from 60–80% down to under 4%. Delivered 2–4× throughput at equal latency. Now universal — every modern engine implements it.
- **Prefix caching** — Hash each KV block by its tokens; reuse on shared prefixes (system prompts, few-shot examples, multi-turn chat history). Almost a free lunch for agentic and RAG workloads. vLLM defaults it on.
- **RadixAttention** (SGLang) — Organize KV cache in a radix tree keyed by token prefixes. Automatic, fine-grained cross-request reuse. Up to 6.4× throughput on agentic workloads.
- **Disaggregated prefill/decode** (Mooncake, FAST 2025 Best Paper; DeepSeek-V3) — Run prefill and decode on separate GPU pools with KV cache transferred over RDMA. The 2026 state of the art at hyperscale.

### Batching

The way a provider groups requests determines whether their GPUs sit idle or sing.

- **Static batching** — Fixed batch, runs until the longest sequence finishes. Wastes compute on finished slots.
- **Continuous batching** (Orca, OSDI 2022) — Re-evaluate batch membership every decode step. Evict finished requests, admit new ones mid-flight. The single biggest throughput unlock of the era, and now the default in every modern serving engine. Also *improves* P50/P90 latency, not just throughput.

### Cost

- **Cost per million tokens** — The headline price. Frontier models: $1–$50 per million output tokens. Commodity open models on competitive providers: $0.10–$5. DeepInfra, Together, and Fireworks are the visible edge of the commoditization curve.
- **Cost per query** — What you actually pay for a realistic request. A 1,000-token input / 500-token output request on GPT-4o costs roughly $0.0075. With 50% cache hit on input, $0.0055.
- **Cache economics** — Anthropic charges separately for cache writes (5-minute vs 1-hour TTLs). OpenAI, Google, and DeepSeek charge only for cache reads. The differences add up fast at agent scale.

## What Different Workloads Optimize For

Not all inference is the same. The metric you care about depends on what you are building.

| Workload | Primary metric | Why |
|---|---|---|
| Chat / assistant | TTFT under 1s + output speed over 50 t/s | Users perceive latency before quality. A 3s TTFT kills the experience even if the answer is perfect. |
| Coding copilot | TTFT + output speed | Latency is everything; long completions get cancelled. |
| **Agentic (multi-step tool loops)** | **P99 per-step latency + output speed** | One slow step stalls the whole chain. Prefix caching matters most here. |
| Reasoning / "thinking" models | Time-to-first-answer + cost-per-task | Reasoning tokens blow up both latency and bill. Quality-per-dollar is the real metric. |
| Batch / offline (eval, ETL, synthetic data) | Aggregate throughput / $ per million tokens | Latency is irrelevant. Quantize harder, batch bigger, use cheaper providers. |
| RAG / long-context | TTFT (scales with prompt length) + context cost | Prefill cost on 100k+ token prompts dominates. Cache-hit pricing is the lever. |
| Structured output (JSON, tool calls) | Constrained-decoding throughput | SGLang's compressed-FSM trick is a real differentiator here. |

## The Benchmark Landscape

Who do you trust to tell you which provider is fastest?

- **Artificial Analysis** — The de facto industry scorecard. Independent, methodology is public, measures P50 over 72 hours across first-party APIs and 19 third-party providers. The benchmark everyone in the market quietly checks.
- **MLPerf Inference (MLCommons)** — Audited hardware benchmarking with strict submission rules. Highest credibility for chip-to-chip comparisons, but benchmarks lag the model frontier.
- **LMSYS Chatbot Arena** — Crowdsourced blind pairwise ranking of model *quality*. The gold standard for "which model is smarter," not "which is faster."
- **Aider LLM Leaderboard** — Coding-specific. The benchmark practitioners actually trust for "which model writes the best code."
- **HF Open LLM Leaderboard** — Automated eval for open-source models. Methodology has been contested and iterated.

Vendor-published benchmarks (NVIDIA claiming x× over A100, Cerebras claiming 10× over H100) should be treated as marketing. Cross-check before citing.

## Key Trends

**Open-model commoditization is real and visible.** Nineteen providers serving the same `gpt-oss-120b` weights is the clearest possible signal. When the same product is available from 19 vendors, the only durable differentiators are price, latency, reliability, and compliance.

**Speed specialists are racing upmarket.** Groq, Cerebras, and SambaNova started as "we serve Llama fast." In 2026 they pitch agentic loops and reasoning-model turn-count as the workload where TPS equals product quality. SambaNova's "3× savings for agentic inference" and Cerebras's "faster inference is the new quality lever" are the same argument: faster TPS lets you trade compute for intelligence at fixed latency.

**Sovereign AI is the dark-horse business model.** SambaNova powers four named sovereign providers (Argyll in the UK, Infercom in the EU, OVHcloud in the EU, SouthernCrossAI in Australia). Cerebras's 2025 revenue was 62% from MBZUAI in the UAE. Saudi Arabia committed $1.5 billion to Groq for a Dammam datacenter. This is a structurally different sale — national-government or regulated-industry customer, data-residency requirement, multi-year hardware lease — that pure-software aggregators cannot play.

**The hyperscaler gateways are quietly the largest aggregators.** AWS Bedrock carries 18 model providers, including direct competitors to each other. Azure Foundry resells Fireworks. The hyperscalers are simultaneously competing with OpenRouter *and* reselling it. Classic cloud-platform squeeze.

**The "aggregator-of-aggregators" inversion.** Fireworks (an aggregator) is resold inside Azure Foundry (a hyperscaler gateway). OpenRouter lists SambaNova (a chip specialist) as a partner provider. Every player is both a buyer and seller of inference capacity depending on the deal.

## The Stalwart

**OpenRouter** is the Stalwart of this layer. A small team built a model marketplace that became the default "model dropdown" for indie AI apps. It does not own any GPUs. It does not train any models. It just routes — but the routing is wrapped in billing, failover, discovery, and developer experience that nobody else has assembled at the same scale. From a $40 million seed+A round in June 2025 to a $1.3 billion valuation twelve months later. The lesson: in a commoditizing market, the marketplace is the moat.

---

*Next: [02-hardware-layer](02-hardware-layer.md)*
