# Reusable AI Research Prompts

Use these prompts with any AI assistant (ChatGPT, Claude, etc.) to accelerate discovery for new verticals.

## Prompt 1: Landscape Mapping

```
I am mapping the software supply chain for [INDUSTRY]. 

Break this down into 3-5 distinct layers from raw input to final output. 
For each layer, identify:
1. What goes in (the raw material)
2. What comes out (the finished product of that layer)
3. 5-7 companies or tools that operate in this layer, including at least 2 open-source options
4. One emerging trend that is changing this layer

Format the output as a structured outline I can paste into markdown files.
```

## Prompt 2: Underdog Discovery

```
In the [INDUSTRY] infrastructure space, everyone knows the giants like [OBVIOUS NAMES].

I am looking for the underdogs: small teams, open-source projects, or niche companies that punch above their weight. 

Find me 5 tools or companies that:
- Are NOT owned by Google, Amazon, Microsoft, or Meta
- Have fewer than 50 employees OR are primarily open-source
- Solve one specific problem exceptionally well
- Are growing in popularity among technical users

For each, explain what they do in one sentence and why they are better than the incumbent solution.
```

## Prompt 3: Trend Synthesis

```
I have the following list of companies in [INDUSTRY]:

[LIST COMPANIES]

Based on this list and your knowledge, what are the 3 most important macro trends shaping this space? 

For each trend:
- Name the trend
- Explain what is driving it
- Identify which companies are winning because of it
- Predict what the landscape looks like in 3 years
```

## Prompt 4: Mermaid Diagram Generation

```
I have a supply chain with the following stages:

[LIST STAGES AND TOOLS]

Generate a Mermaid flowchart (graph LR) that visualizes this flow. 

Requirements:
- Use subgraphs to group related tools
- Highlight open-source tools with a distinct color
- Keep labels short (under 5 words)
- Use the syntax that renders natively on GitHub
```

## Prompt 5: Report Summarization

```
I will paste a long article, report, or transcript below. 

Extract:
1. All company or tool names mentioned
2. Any funding amounts or valuation numbers
3. Key statistics about market size or growth
4. The author's main argument or thesis
5. One surprising fact I might have missed

Format as a structured summary with bullet points.

[PASTE TEXT HERE]
```

## Prompt 6: Verification Checklist

```
I am researching [COMPANY NAME] in the [INDUSTRY] space.

Help me verify the following:
1. Is their website active and loading?
2. When was their last blog post or product update?
3. Are they still an independent company, or were they acquired?
4. What is their primary open-source project (if any) and when was the last commit?
5. Do they have a real use case, or is this just a landing page with no product?

If you cannot verify something, say "REQUIRES MANUAL CHECK" instead of guessing.
```

## Prompt 7: Stalwart Story Generation

```
I found a tool called [TOOL NAME]. It does [BRIEF DESCRIPTION].

Help me write a 2-paragraph "Stalwart" story for my Digital Commodities Atlas. 

Paragraph 1: What the tool does and why it matters
Paragraph 2: Why it is surprising that a small team built this, and what it says about the industry

Tone: Accessible, enthusiastic, slightly irreverent. Write for a smart 22-year-old who is not a programmer.
```

## Prompt 8: Competitor Matrix

```
In the [INDUSTRY] space, I want to compare [COMPANY A] and [COMPANY B].

Create a comparison table with the following rows:
- Founded year
- Open source vs proprietary
- Primary use case
- Key differentiator
- Pricing model (if known)
- Community size (GitHub stars, forum activity, etc.)
- Best for (type of user/company)

Add a 2-sentence recommendation at the bottom: who should choose which tool and why.
```

---

## Usage Tips

- **Always verify AI outputs.** AI is excellent at generating hypotheses but terrible at knowing which companies are dead or acquired.
- **Combine prompts.** Run Prompt 1 to get the landscape, then run Prompt 2 on each layer to find underdogs.
- **Save good outputs.** When a prompt produces gold, save the result in your vertical folder as a `.md` file with a note about which prompt generated it.
- **Iterate.** The first output is never perfect. Refine prompts based on what was missing.
