# 1. Text & NLP Annotation

## What Goes In

Raw text: documents, conversations, social media posts, medical records, legal contracts, scientific papers, code repositories, and web pages. Unstructured language in its natural form.

## What Comes Out

Structured text with labels, spans, classifications, or rankings that teach models to understand language, generate responses, classify intent, or extract information.

## Annotation Types

### Named Entity Recognition (NER)
Identifying and classifying named things in text: people, organizations, locations, dates, products. The foundation of information extraction.
- **Example**: In "Apple Inc. was founded by Steve Jobs in Cupertino," tag `Apple Inc.` as ORG, `Steve Jobs` as PERSON, `Cupertino` as LOCATION.

### Sentiment Analysis
Classifying the emotional tone or opinion expressed in text.
- **Example**: "This product is amazing" → Positive; "Terrible customer service" → Negative.

### Intent Classification (Chatbots)
Categorizing what a user wants to achieve with their message.
- **Example**: "Book a flight to Paris" → intent: `book_flight`.

### Question-Answering Pairs
Creating question-and-answer combinations from documents, used to train retrieval-augmented generation (RAG) systems.
- **Example**: From a Wikipedia article, generate "When was the Eiffel Tower built?" → "1887-1889."

### Summarization
Condensing long documents into shorter versions while preserving key information.
- **Example**: A 5-page news article → 3-sentence summary.

### Toxicity / Content Moderation
Flagging harmful, offensive, or policy-violating content.
- **Example**: Rating text on scales for hate speech, harassment, misinformation, sexual content.

### Code Annotation (Instruction-Following)
Labeling code snippets with natural language instructions or explanations, used to train code completion and generation models.
- **Example**: "Write a Python function to reverse a string" → labeled solution with explanation.

### RLHF: Reinforcement Learning from Human Feedback
A specialized form of text annotation where humans compare multiple model outputs and rank them by quality, helpfulness, or safety. This trains models like ChatGPT and Claude to align with human preferences.
- **Example**: Given two answers to "How do I bake bread?", rank which is more accurate, helpful, and harmless.

## Key Players

| Name | URL | Role |
|------|-----|------|
| Scale AI | https://scale.com | RLHF and general text annotation at scale |
| Surge AI | https://www.surgehq.ai | Specialized RLHF and preference data |
| Appen | https://appen.com | Multilingual NLP annotation, search relevance |
| Hive | https://thehive.ai | Content moderation datasets and API |
| Outlier (new entrant) | https://outlier.ai | Emerging RLHF-focused platform |
| Label Studio | https://labelstud.io | Self-hosted text annotation (open source) |
| Argilla | https://argilla.io | NLP feedback loops and RLHF tooling |
| Doccano | https://doccano.github.io/doccano | Simple text annotation for NER and sentiment |

## Open vs. Proprietary

**Mixed**. Many foundational text datasets (CoNLL, GLUE, SQuAD) are open and academic. Commercial RLHF and moderation annotation is almost entirely proprietary, locked inside AI labs and specialized vendors.

## The Stalwart

**Argilla**. A small open-source team from Spain built a feedback platform specifically for NLP and LLM fine-tuning. It handles everything from sentiment labeling to RLHF preference ranking, self-hosted, with a growing community in the LLM space. Competes with enterprise tools charging thousands per month.

## TBD

- Constitutional AI and self-critique: how much human annotation is replaceable?
- Synthetic text generation for training: using LLMs to generate and label their own training data
- Multilingual annotation gaps: most tools and datasets are English-centric
- Legal and regulatory text annotation: contract review, compliance checking, case law extraction

---

*Back to [overview](00-overview.md)*
