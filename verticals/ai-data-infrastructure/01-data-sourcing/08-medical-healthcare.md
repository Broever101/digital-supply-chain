# 8. Medical / Healthcare

## What Goes In

Patient records, medical images, clinical trial results, insurance claims, and real-world health outcomes. The most sensitive and regulated data in the AI supply chain.

## What Comes Out

De-identified, annotated, and often federated datasets used to train diagnostic models, predict disease progression, and discover new treatments.

## Key Players

| Name | URL | Role |
|------|-----|------|
| TCIA (The Cancer Imaging Archive) | https://www.cancerimagingarchive.net | NIH-hosted medical imaging repository |
| Flywheel | https://flywheel.io | Data management and curation for medical research |
| MD.ai | https://www.md.ai | Medical imaging annotation and model deployment |
| NVIDIA Clara | https://www.nvidia.com/en-us/healthcare | Federated learning for healthcare AI |
| Tempus | https://www.tempus.com | Clinical and molecular data platform for precision medicine |
| Cerner / Oracle Health | https://www.oracle.com/health/ | EHR systems that hold massive patient datasets |
| Epic Systems | https://www.epic.com | Dominant US EHR provider |
| TriNetX | https://www.trinetx.com | Real-world evidence network from EHR data |

## Open vs. Proprietary

**Mixed and heavily regulated**. Some imaging datasets (TCIA, ChestX-ray14) are open. EHR and claims data are locked behind HIPAA, GDPR, and hospital contracts. The value is in access, not the data itself — whoever can legally aggregate medical data at scale has a massive moat.

## The Stalwart

**TCIA (The Cancer Imaging Archive)**. Run by the National Cancer Institute, it hosts over 30 million de-identified medical images across dozens of cancer types, completely free, with standardized formats and rich metadata. Small research teams around the world train diagnostic models on this data that would otherwise require partnerships with billion-dollar hospital systems.

## TBD

- HIPAA de-identification standards and their limitations for AI training
- The rise of federated learning: train models without centralizing patient data
- Synthetic medical data as a privacy-preserving alternative
- The EHR duopoly: Epic and Cerner control most US patient data

---

*Back to [overview](00-overview.md)*
