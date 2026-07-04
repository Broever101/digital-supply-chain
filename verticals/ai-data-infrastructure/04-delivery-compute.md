# Layer 4: Delivery to Compute

## What Goes In

A cleaned, labeled, versioned dataset. Often terabytes or petabytes in size.

## What Comes Out

That same data, loaded into GPUs or specialized AI chips, ready for a model to train on.

## Who Does It

### The GPU Clouds
Training modern AI models requires thousands of specialized chips. Most companies cannot afford to own them:
- **Lambda Labs** — A cloud provider that rents GPUs by the hour, specifically optimized for machine learning. Popular with startups and researchers who need H100s or A100s without a million-dollar upfront investment.
- **CoreWeave** — Built by former cryptocurrency miners who pivoted to AI cloud infrastructure. They own one of the largest private GPU fleets in the world and rent them to AI labs.
- **Together AI** — Focuses on distributed training across many smaller GPUs, making large-model training more accessible.

### The Hyperscalers
The big clouds want to own the entire stack:
- **AWS SageMaker** — Upload your dataset, pick a model architecture, and AWS handles the training infrastructure.
- **Google Vertex AI** — Google's equivalent, tightly integrated with TensorFlow and Google's custom TPU chips.
- **Azure Machine Learning** — Microsoft's offering, popular with enterprises already using Office 365 and Azure services.

### The On-Premise Option
Some companies — especially in defense, finance, and healthcare — cannot send data to the cloud:
- **NVIDIA DGX** — A "supercomputer in a box" that companies buy and install in their own data centers. Costs hundreds of thousands of dollars but keeps data inside the building.

## Key Trends

**The compute bottleneck is becoming the data bottleneck.** We used to worry about not having enough GPUs. Now we worry about not having enough high-quality data to feed them. This is why data infrastructure — the focus of this entire vertical — is suddenly a strategic priority.

**Specialized chips beyond GPUs.** NVIDIA dominates today, but Google TPUs, Amazon Trainium, and custom silicon from startups like Cerebras are challenging the monopoly. Each chip requires its own data pipeline optimizations.

## The Stalwart

**Lambda Labs**. Started as a GPU workstation builder for deep learning students, grew into a cloud provider that undercuts AWS on price for ML workloads. They do not have the marketing budget of Amazon, but researchers and startups know them as the place to get affordable GPU access without enterprise contracts.

---

*Back to [00-executive-summary](00-executive-summary.md)*
