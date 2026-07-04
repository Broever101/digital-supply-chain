# Layer 3: Pipeline Orchestration and Quality Control

## What Goes In

Labeled datasets. But also: code, experiment configurations, model weights, and metadata about who labeled what and when.

## What Comes Out

A reproducible, versioned, auditable pipeline that turns raw data into a training-ready dataset. If something goes wrong, you can rewind to any step and see exactly what changed.

## Who Does It

### Workflow Orchestration
These tools schedule and manage the steps in a data pipeline:
- **Apache Airflow** — The grandfather of data pipelines. Created by Airbnb, now an Apache open-source project. You define your pipeline as code, and Airflow makes sure each step runs in the right order at the right time.
- **Prefect** — A modern alternative to Airflow. Built by former Airflow developers who wanted something simpler and more Pythonic.
- **Dagster** — Treats data pipelines like software. Each step has typed inputs and outputs, making it easier to test and debug.

### Data Versioning and Lineage
When a dataset has thousands of versions, you need a "Git for data":
- **DVC (Data Version Control)** — An open-source tool that versions datasets alongside your code. If your model performance drops, DVC tells you exactly which dataset version caused it.
- **MLflow** — Originally from Databricks, now an open-source standard. Tracks experiments, model versions, and parameters.
- **Weights & Biases (WandB)** — A commercial platform that logs experiments, visualizes training runs, and compares model versions. Popular with researchers and deep learning teams.

### Quality Frameworks
How do you know your labels are good?
- **Cohen's Kappa** — A statistical measure of how much two human labelers agree. If they disagree constantly, your labeling guidelines are probably unclear.
- **NIST AI Risk Management Framework** — A government guide for detecting bias, ensuring fairness, and documenting data quality. Boring but essential for any AI system that affects real people.

## Key Trends

**Pipelines are becoming code.** Just as infrastructure moved from manual server setup to "infrastructure as code," data pipelines are now defined in Python or YAML files. This means data scientists can use the same tools — version control, code review, automated testing — that software engineers have used for decades.

**Lineage is becoming a legal requirement.** In regulated industries like healthcare and finance, you must prove you know where your training data came from and how it was transformed. Tools like DVC and MLflow are becoming compliance infrastructure, not just convenience tools.

## The Stalwart

**DVC (Data Version Control)**. Built by a small open-source team, it solves a problem that every AI lab faces: "Which version of the dataset trained this model?" It integrates with Git, works with any cloud storage, and is completely free. A tool this fundamental should be owned by a giant, but it is maintained by a dedicated community.

---

*Next: [04-delivery-compute](04-delivery-compute.md)*
