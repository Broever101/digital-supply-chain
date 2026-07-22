# Layer 5: Application and Control Plane

## What Goes In

API requests. A billing relationship with a customer. The need to observe, route, evaluate, and govern inference at scale.

## What Comes Out

Billed, observable, routed, governed tokens — wrapped in a developer experience that turns raw inference into a usable product.

This is the layer developers actually touch. Almost nobody using ChatGPT or Cursor thinks about vLLM or NVLink. They think about API keys, rate limits, monthly bills, latency dashboards, and which model to pick for which task. This layer exists to make all of that work.

It is also the layer that consolidated most aggressively in 2026. Standalone usage-billing companies were acquired into payment rails. The leading observability startup was acquired by a data warehouse. And yet the open-source projects behind these companies are more active than ever.

## Who Does It

### Part A — Gateways, Routers, Proxies

The "front door" of any AI application. These normalize the chaos of dozens of incompatible provider APIs into one consistent interface.

- **LiteLLM** (BerriAI) — **54,054 stars**, custom license, pushed today (active daily). Y Combinator W23. Rust core + Python SDK. Routes to 100+ providers behind an OpenAI-compatible interface. Adopters include **Stripe, Netflix, OpenAI Agents SDK, Google ADK, OpenHands**. The de facto open-source winner. *Implicated in the Mercor supply-chain breach (April 2026) — gateways are now critical-infra attack surface, not just developer convenience.*
- **OpenRouter** — The largest "model marketplace." 400+ models from 70+ underlying providers, 5.5% fee on pay-as-you-go. **$113 million Series B at $1.3 billion valuation, CapitalG-led, May 2026.** Weekly throughput: 25 trillion tokens, 5× year-over-year. Co-founded by the OpenSea founders. The default "model dropdown" for indie AI apps.
- **Vercel AI SDK** — **25,669 stars**, custom license. TypeScript-first router. The default for the Next.js ecosystem. Less feature-complete than LiteLLM but tightly integrated into Vercel's deployment story.
- **Portkey** — **12,471 stars**, MIT. Gateway + guardrails (1,600+ LLMs, 50+ guardrails). MIT-licensed gateway is the wedge; paid observability/control plane is the business.
- **Helicone** — **5,970 stars**, Apache-2.0. Y Combinator W23. Hybrid observability + proxy.
- **RouteLLM** — **5,217 stars**, Apache-2.0. **Last push August 2024 — stale.** Research artifact from the lm-sys routing paper, not maintained as a product. Signal: pure cost-routing research does not sustain a project.
- **Martian** — **Pivoted away from routing.** Now an AI-interpretability / mech-interp research lab (ARES online-RL for coding agents, code-review benchmark). Team ex-DeepMind/Anthropic/Meta. The original "smart model router" thesis did not survive.
- **Not-diamond** — Router startup. Status and funding unverifiable this session.
- **Cloudflare AI Gateway** — Cloudflare's edge proxy for AI APIs. Bundled with Cloudflare Workers AI.
- **Kong AI Gateway, APISIX AI Gateway, Tyk AI Gateway** — Legacy API gateway vendors adding AI features. Bundled with their existing enterprise foothold.
- **Together AI, Groq, DeepInfra** — First-party inference providers that also function as open-model routers via their broad model catalogs.

**The pattern.** Pure routing logic commoditizes to zero. The marketplace (OpenRouter) and the proxy-with-billing-observability (LiteLLM) are where value accrues.

### Part B — Billing, Metering, API Keys

The most dramatic consolidation story in the entire stack.

> **Headline:** The standalone usage-billing category was acquired out of existence in roughly six months.

- **Metronome** → **ACQUIRED BY STRIPE (~$1 billion)**, announced December 2, 2025, closed January 2026. Customers: **OpenAI, Anthropic, Databricks, NVIDIA**. Ex-Dropbox founders. Last independent valuation ~$470 million / $128 million raised.
- **Orb** (orb.dev) → **ACQUIRED BY ADYEN ($335 million, all-cash)**, announced June 11, 2026, closed ~July 1, 2026. Adyen primary-source press release. Customers: Vercel, Replit, Supabase, Glean.
- **m3ter** → **ACQUIRED BY SALESFORCE.** Completes the trio.
- **Lago** — **10,236 stars**, AGPL-3.0, pushed July 16, 2026. The leading independent metering/billing option post-consolidation. Open-source, payment-processor-neutral. The Stalwart survivor.
- **OpenMeter** — Repo is a 1-star 2020 placeholder. Site returned empty. Independence and viability unclear.
- **Amberflo** — Status unverifiable.
- **Unkey** — **5,397 stars**, custom license, pushed today. API-key management + rate limiting. Go. Active and focused.
- **Stripe Metered Billing** — First-party. Now supercharged by the Metronome acquisition. Stripe is the default for AI metering.
- **tiktoken** (OpenAI) — **18,782 stars**, MIT. The de-facto token counter. Foundation library that every metering system leans on.
- **Hugging Face tokenizers** — **10,896 stars**, Apache-2.0, Rust core. The other major tokenization library.

**The lesson.** Anyone building "Stripe-for-AI-billing" today is building a feature Stripe already owns. The category is closed.

### Part C — Observability and Evaluation

How you know your inference is actually working: tracing, evals, prompt management, red-teaming.

- **Langfuse** — **31,449 stars**, custom license (ELv2-style), pushed July 19, 2026. **Commercial entity ACQUIRED BY CLICKHOUSE January 2026**, alongside ClickHouse's $400 million Series D at a $15 billion valuation (Dragoneer-led). Seed of ~$4 million from Lightspeed, La Famiglia, Y Combinator W23. **The OSS repo remains hyperactive** — the "acquired company, living project" pattern. This is what the post-consolidation landscape looks like.
- **promptfoo** — **23,420 stars**, MIT, pushed July 19, 2026. Red-teaming + eval. **"Used by OpenAI and Anthropic"** per the README. Quietly one of the most strategically positioned OSS projects in this layer.
- **ragas** — **14,914 stars**, Apache-2.0. RAG evaluation standard. (Repo moved from `explodinggradients` to `vibrantlabsai`.)
- **Arize Phoenix** — **10,633 stars**, custom license. The leading *independent* eval platform post-Langfuse-acquisition.
- **OpenLLMetry** (Traceloop) — **7,311 stars**, Apache-2.0. OpenTelemetry-native. The "boring, interoperable" enterprise choice. Wins deployments that refuse proprietary tracing.
- **W&B Weave** — 1,108 stars. Commercial-first; benefits from W&B's massive distribution. Low star count is misleading.
- **Braintrust** — Commercial eval platform, active. SDK stars aren't a useful signal. Funding unverified this session.
- **LangSmith** (LangChain) — Commercial. Traction not verified.
- **Datadog LLM Observability, New Relic AI Monitoring, Honeycomb for LLMs** — Incumbent APM vendors bolting on LLM tracing. Specific market share not verifiable.
- **Parea, Lunary, Vellum, Humanloop** — Smaller players; traction not verified.

### Part D — Benchmarking and Discovery

How users pick which model and which provider.

- **LMSYS Chatbot Arena** — **39,492 stars**, crowdsourced blind pairwise ranking. The gold standard for "which model is smarter." Academic/nonprofit governance gives it authority that vendor benchmarks lack.
- **Artificial Analysis** — Independent benchmarking (price, latency, quality). Publishes "State of AI" reports. The de facto provider-leaderboard reference. Funding details gated; independence claims credible based on methodology transparency.
- **MLPerf Inference** (MLCommons) — Hardware/system benchmarking. Strict submission rules, audited. Relevant for *providers* and *chip vendors*, less so for end users.
- **Hugging Face Open LLM Leaderboard** — Automated eval for OSS models. Methodology has been contested and iterated.
- **Aider LLM Leaderboard** — Coding-specific. The benchmark practitioners actually trust for "which model writes the best code." Part of Aider (47.5k stars).

### Part E — Developer Experience and Consumer-of-Inference

The demand sink. The reason all of the above exists.

- **Cline** — **64,821 stars**, Apache-2.0. Now the largest OSS coding agent by stars, pushed daily. Dark-horse winner of 2025–26.
- **Aider** — **47,523 stars**, Apache-2.0. Terminal pair-programmer. Runs the coding LLM leaderboard.
- **Continue** — **34,977 stars**, Apache-2.0. OSS coding agent. Multi-model by design → a major gateway demand driver.
- **Cursor, Bolt, Replit Agent, ChatGPT, Claude.ai, Gemini consumer apps** — Proprietary. The revenue-side demand sink. Their multi-provider needs pull through OpenRouter, LiteLLM, and the entire control-plane layer.

## Key Trends

**The 2026 control-plane consolidation is the defining story of this layer.** In six months, three of the leading standalone billing companies (Metronome, Orb, m3ter) were acquired by Stripe, Adyen, and Salesforce. The leading observability company (Langfuse) was acquired by ClickHouse. The pattern: standalone point solutions get absorbed into broader platforms. **The OSS projects outlived their commercial parents** — Langfuse-the-repo is more active than ever after the ClickHouse acquisition.

**Open source owns the developer-facing control plane.** LiteLLM (54k), Vercel AI SDK (25.7k), promptfoo (23.4k), Langfuse (31k), tiktoken (18.8k). Commercial players (Portkey, Helicone, Braintrust) monetize hosted/enterprise/guardrails on top, not the core. Anyone planning to build a new gateway or observability tool in 2026 is competing with mature OSS projects that have 20,000+ stars and daily commits.

**Routing is a feature; the marketplace is the business.** RouteLLM is stale (a paper, not a product). Martian quit routing for interpretability research. Yet OpenRouter hit $1.3 billion — because it is an *exchange* (failover + billing + discovery + BYOK markup), not a router. Pure routing logic commoditizes to zero; the surrounding transaction layer captures value.

**Coding agents are bigger than the infra under them.** Cline 64.8k + Aider 47.5k + Continue 35k — these consumers-of-inference dwarf most gateways and are the real routing and billing demand engines. An atlas of AI inference that ignores the demand side misreads where gateway adoption comes from.

**Gateways are now critical-infra attack surface.** The Mercor supply-chain incident (April 2026, "thousands of companies impacted" via LiteLLM) reframes proxies from convenience to security-critical dependency. Expect this to become a first-order concern for any team running inference in production.

**Margin compression drives the differentiation shift.** As token prices fall, gateways compete on reliability, caching, guardrails, and SLOs — not routing cleverness. Portkey's guardrails and OpenRouter's provider-failover are the durable moats. The category is converging on "gateway + observability + guardrails + billing" as a single integrated product.

## The Stalwarts

Multiple in this layer:

- **Lago** — the biggest independent billing survivor precisely because it is OSS / AGPL and payment-processor-neutral. 10k stars post-consolidation. The Stalwart of the billing category.
- **promptfoo** — used internally by OpenAI *and* Anthropic for red-teaming. 23k stars but oddly under-discussed vs. Langfuse. The Stalwart of evaluation.
- **OpenLLMetry** — the OTel-native pick; wins enterprise deployments that refuse proprietary tracing. The "boring infrastructure" Stalwart.
- **Cline** — the dark-horse coding agent that quietly lapped everyone. 64.8k stars, daily pushes,Apache-2.0. The Stalwart of the demand side.
- **Aider LLM Leaderboard** — the coding-quality benchmark practitioners actually trust. Maintained by the Aider team; small, focused, indispensable.
- **tiktoken** — the unglamorous token-counting foundation everything metering depends on. The most-deployed library in this entire layer, by install count.

## The Hidden Architecture

Step back and notice the macro pattern: this entire layer is converging on **gateway + observability + billing + guardrails** as one integrated product. LiteLLM is adding observability. Helicone already has both. Portkey adds guardrails. Langfuse is adding more gateway features. The standalone categories are collapsing into one.

If you are building in this layer today, the question is not "which feature do I build?" but "which integrated suite do I compete with or plug into?" The standalone point solutions had their moment in 2023–24. The integrated suite era is now.

---

*This concludes the AI Inference vertical. See [diagram.mmd](diagram.mmd) for the visual flowchart and [players.json](players.json) for the structured database of companies and tools.*
