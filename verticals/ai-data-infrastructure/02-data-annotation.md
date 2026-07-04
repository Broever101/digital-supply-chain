# Layer 2: Data Annotation and Labeling

## What Goes In

Raw data. Images without tags. Videos without descriptions. Text without categories. Audio without transcripts. A self-driving car camera captures millions of frames per day, but the machine does not know what a "pedestrian" is until a human (or a very smart algorithm) draws a box around one and says "this is a person."

## What Comes Out

Training-ready datasets. Every pixel, word, or sound has been categorized, ranked, or annotated so a model can learn from it. This is the refinery layer of the AI supply chain.

## Who Does It

### The Giants
These are the companies that built the modern AI industry by labeling data at massive scale:
- **Scale AI** — The best-known name in the space. Labels data for autonomous vehicles, defense, and major AI labs. Valuation in the tens of billions. Think of them as the Foxconn of data: they have built a global workforce plus software to manage it.
- **Appen** — An Australian company that has been doing crowdsourced data work since before "AI" was a buzzword. They provide labeled data for search engines, voice assistants, and more.
- **TELUS International** — A Canadian outsourcing giant that expanded into AI data services. They run annotation centers around the world with strict quality control.

### The Platform-First Companies
These firms sell software that lets companies build their own labeling workflows:
- **Labelbox** — Backed by Andreessen Horowitz. An enterprise "data factory" where teams upload raw data, define labeling tasks, and manage quality without outsourcing everything.
- **SuperAnnotate** — Focused on computer vision. Built-in tools for image segmentation, video tracking, and model-assisted labeling.
- **Encord** — Combines labeling with active learning: the platform suggests which images need human attention most, saving time and money.
- **Dataloop** — An end-to-end platform covering annotation, automation, and model training in one place.

### The Vertical Specialists
These companies know one domain deeply:
- **iMerit** — Specializes in medical imaging and autonomous driving. Their annotators are trained to spot tumors or road hazards with clinical precision.
- **Sama** — A certified B-Corp that combines ethical AI sourcing with high-quality annotation. They hire workers in East Africa and India with above-market wages.
- **CloudFactory** — Blends software with a managed workforce, focused on long-term client relationships rather than one-off projects.

### The Cloud Giants
The big cloud providers do not want you to leave their ecosystem:
- **AWS SageMaker Ground Truth** — Amazon's built-in labeling service. You upload data, define tasks, and AWS handles the workforce or pre-labeling.
- **Azure Machine Learning Data Labeling** — Microsoft's equivalent, tightly integrated with Azure's ML tools.

### The Open Source Tools
These let a solo developer or small team label data without paying enterprise prices:
- **Label Studio** (by Heartex) — The most popular open-source labeling tool. Supports images, text, audio, and video. You host it yourself.
- **CVAT** (Computer Vision Annotation Tool, by Intel) — Built for video and image annotation at scale. Used by researchers and startups worldwide.
- **Argilla** — Focused on text and NLP data. Lets teams collaborate on labeling and feedback loops for language models.

## Key Trends

**The rise of AI-assisted labeling.** The old model was: human looks at image, human draws box, human repeats one million times. The new model is: AI draws 800,000 boxes automatically, human corrects the 200,000 hardest ones. This hybrid approach cuts costs by 40 to 60 percent while maintaining or improving quality. Scale AI, Labelbox, and TELUS all emphasize this "human-in-the-loop" approach.

**Quality over quantity.** Early AI training used whatever labeled data was cheap. Now, a smaller, perfectly labeled dataset often beats a massive, messy one. This is why specialist companies like iMerit charge premium prices for medical data.

## The Stalwart

**Label Studio** by Heartex. A small team built an open-source labeling tool that competes with platforms charging tens of thousands of dollars per year. It is self-hosted, community-driven, and extensible. A single engineer can set it up in an afternoon and start labeling data for a computer vision startup. No sales calls, no annual contracts.

---

*Next: [03-pipeline-orchestration](03-pipeline-orchestration.md)*
