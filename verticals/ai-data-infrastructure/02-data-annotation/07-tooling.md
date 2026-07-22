# 7. Tooling Layer

## What Goes In

Raw, unlabeled data and a team of annotators. Also: project requirements, annotation guidelines, quality standards, and deadlines.

## What Comes Out

Structured, versioned, exportable labeled datasets in formats ready for model training (COCO, Pascal VOC, YOLO, JSON, CSV, TFRecords).

## The Tooling Spectrum

### Open-Source Labeling Tools

| Tool | URL | Best For | License |
|------|-----|----------|---------|
| **Label Studio** | https://labelstud.io | Custom workflows, text/NLP, images, audio | Apache 2.0 |
| **CVAT** | https://www.cvat.ai | Computer vision, bounding boxes, segmentation, video | MIT |
| **Argilla** | https://argilla.io | NLP feedback, RLHF, text classification | Apache 2.0 |
| **Doccano** | https://doccano.github.io/doccano | Simple text annotation (NER, sentiment) | MIT |
| **Diffgram** | https://diffgram.com | Visual data ops, open-core model | Apache 2.0 (core) |

**Label Studio** is the most versatile: self-hosted, supports every modality, extensible via plugins, and has an active community. It is the default choice for teams that need control over their data and do not want vendor lock-in.

**CVAT** is the computer vision standard: built by Intel, optimized for bounding boxes and segmentation, supports video tracking, and has a web-based interface that teams can deploy internally.

### Commercial Annotation Platforms

| Platform | URL | Differentiator |
|----------|-----|----------------|
| **Labelbox** | https://labelbox.com | Enterprise workflow automation, model-assisted labeling |
| **SuperAnnotate** | https://www.superannotate.com | Pixel-accurate tools, medical and AV focus |
| **Encord** | https://encord.com | Active learning, data-centric AI, quality analytics |
| **Dataloop** | https://dataloop.ai | End-to-end: annotation + pipeline + model management |
| **V7 Labs** | https://www.v7labs.com | Auto-annotation, developer-friendly API |

**Labelbox** dominates the enterprise segment with workflow automation: define tasks, assign annotators, review quality, and export to training pipelines in one platform.

**Encord** focuses on "data-centric AI" — using active learning to identify which unlabeled samples will most improve model performance, reducing annotation costs by focusing human effort where it matters most.

### Cloud-Native / Integrated MLOps

| Platform | URL | Integration |
|----------|-----|-------------|
| **AWS SageMaker Ground Truth** | https://aws.amazon.com/sagemaker/groundtruth | Native AWS, workforce management |
| **Azure ML Data Labeling** | https://azure.microsoft.com/products/machine-learning | Native Azure, ties into ML pipelines |
| **Google Cloud Data Labeling** | https://cloud.google.com/data-labeling | Native GCP, connects to Vertex AI |

These are convenient if you already live in a cloud ecosystem, but they are less flexible than dedicated annotation platforms.

## Key Capabilities to Evaluate

| Capability | Why It Matters |
|------------|--------------|
| **Self-hosting** | Keep sensitive data inside your infrastructure |
| **Multi-modal support** | Handle text, images, audio, and 3D in one platform |
| **Workflow automation** | Route tasks, enforce review cycles, manage annotator pools |
| **Model-assisted labeling** | Use AI to pre-label, reducing human effort 40-70% |
| **Quality control** | Inter-annotator agreement, gold standard checks, consensus mechanisms |
| **API and integrations** | Connect to your existing ML pipeline (S3, GCS, Azure Blob, MLflow) |
| **Active learning** | Model tells you what to label next |
| **Versioning** | Track changes to labels and guidelines over time |

## The Stalwart

**Label Studio** by Heartex. An open-source tool that competes with platforms charging tens of thousands of dollars per year. It is self-hosted, community-driven, extensible, and supports every major annotation task. A single engineer can set it up in an afternoon and start labeling data for a computer vision startup. No sales calls, no annual contracts, no vendor lock-in.

## TBD

- The convergence of annotation and MLOps: will separate annotation tools disappear into end-to-end platforms?
- Real-time collaboration: multiple annotators working on the same dataset simultaneously
- Annotation marketplaces: platforms that connect projects with freelance annotators (Amazon Mechanical Turk evolution)
- Synthetic labeling integration: generating labels automatically from simulations and refining with human review
- Privacy-preserving annotation: labeling encrypted or federated data without seeing the raw content

---

*Back to [overview](00-overview.md)*
