# Research Methodology

## The 70/30 Rule

We use AI aggressively for **structure and synthesis**, but verify everything for **facts and existence**.

| Task | AI Role | Human Role |
|------|---------|------------|
| Initial landscape mapping | Generate category hypotheses, find company names from prompts | Verify company still exists, check if acquired or dead |
| Reading long reports | Summarize 50-page PDFs, extract company names and metrics | Cross-check numbers against original source |
| Structuring notes | Convert messy research into standardized markdown templates | Add nuance, remove AI hallucinations |
| Diagram generation | Create Mermaid/Graphviz syntax for supply chain visuals | Validate relationships and dependencies |
| Writing first drafts | Generate "overview" paragraphs from bullet points | Rewrite for voice, add discovered insights |

## Discovery Toolkit

### Company and Tool Discovery
- **Crunchbase + PitchBook** — Filter by category and funding stage. Look for Series A-C companies: proven but still under the radar.
- **StackShare** — See what tech stacks real companies use. Search "Stripe" and discover their internal tools.
- **AlternativeTo** — Find alternatives to known tools. "Alternative to SendGrid" leads you to Stalwart-like mail servers.
- **Y Combinator Company Directory** — Filter by batch and sector. Recent batches have incredibly specific infrastructure tools.
- **GitHub Trending + Awesome Lists** — `awesome-selfhosted`, `awesome-sysadmin`, `awesome-data-engineering` are goldmines.

### Industry Intelligence
- **VC Market Maps** — Bessemer's "State of the Cloud," a16z's market maps, Sequoia's "Arc" updates. VCs pay analysts to map sectors so you do not have to.
- **Gartner Magic Quadrants / Forrester Waves** — Boring but comprehensive for established categories.
- **Conference Talks (YouTube)** — KubeCon (cloud-native), re:Invent (AWS), FOSDEM (open source), Strange Loop (deep tech, archived but invaluable).
- **Podcasts** — "Software Engineering Daily," "The Changelog," "CoRecursive." Founders explain their niche infrastructure in detail.

### The Underbelly Sources
- **Hacker News "Show HN"** — Filter by `showhn` tag. Solo developers launch precise tools here.
- **Lobste.rs** — Smaller, more technical than HN.
- **Indie Hackers** — Revenue-transparent small SaaS tools.
- **Newsletters** — "Software Stack Investing" (infrastructure), "The Pragmatic Engineer."

## Verification Checklist

Before any company or tool is written into a vertical, it must pass:
- [ ] Website loads and looks active (not a zombie project)
- [ ] Founded or last major update within the last 5 years (exceptions allowed for timeless OSS)
- [ ] Has a real use case (not just a GitHub repo with no README)
- [ ] Funding status verified (or marked as TBA if unknown)

## How We Write

- One idea per paragraph
- No jargon without a one-sentence explanation
- Every layer must answer: "What goes in, what comes out, and who does it?"
- Highlight the Stalwarts: small, precise, often open-source tools that punch above their weight

## AI Prompts

See `.ai-prompts/research-prompts.md` for reusable prompts you can run for any new vertical.
