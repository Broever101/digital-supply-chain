# 6. Medical / Healthcare

## What Goes In

Medical images, clinical records, pathology slides, genomic sequences, and wearable sensor data generated during patient care.

## What Comes Out

Expert-labeled medical datasets that train models to detect diseases, segment organs, predict outcomes, and assist clinical decision-making.

## Why This Domain Is Different

Medical annotation sits at the extreme end of the complexity spectrum:
- **Expert-only**: Only trained radiologists, pathologists, and clinicians can annotate accurately
- **High stakes**: A missed tumor annotation is not just an error — it is a potential life-or-death mistake
- **Regulatory scrutiny**: FDA and EMA require traceable, validated training data for clinical AI
- **Privacy lockdown**: HIPAA, GDPR, and hospital policies make data access extremely difficult
- **Cost**: Medical annotation costs $5-50 per image vs. $0.05-0.20 for general bounding boxes

## Annotation Tasks Specific to Medical AI

### Medical Image Segmentation
Outlining organs, tumors, lesions, and anatomical structures in radiology and pathology images.
- **Example**: Segmenting a lung nodule in a CT scan; outlining a tumor boundary in an MRI.

### Lesion Detection and Classification
Identifying suspicious regions and classifying them by severity or type.
- **Example**: Classifying a skin lesion as benign, suspicious, or malignant.

### Pathology Slide Annotation
Labeling regions on whole-slide images (WSI) that can be tens of gigabytes in size.
- **Example**: Identifying cancerous tissue regions on a histopathology slide.

### Clinical Entity Extraction
Extracting medical concepts from clinical notes: diagnoses, medications, procedures, and lab results.
- **Example**: From "Patient presents with chest pain, prescribed aspirin 81mg," extract `chest pain` (symptom), `aspirin` (medication), `81mg` (dosage).

### Temporal Event Annotation
Tracking disease progression and treatment response over time.
- **Example**: Labeling when a patient was diagnosed, when treatment started, and when remission occurred.

## Key Players

| Name | URL | Role |
|------|-----|------|
| iMerit | https://imerit.net | Medical imaging annotation with expert workforce |
| MD.ai | https://www.md.ai | Medical imaging annotation and model deployment |
| Centaur Labs | https://www.centaur labs.com | Gamified medical annotation platform |
| Flywheel | https://flywheel.io | Medical data management and curation |
| NVIDIA Clara | https://www.nvidia.com/en-us/healthcare | Federated learning for medical AI |
| Tempus | https://www.tempus.com | Clinical and molecular data platform |
| TCIA (NIH) | https://www.cancerimagingarchive.net | Open medical imaging repository |
| Encord | https://encord.com | Medical annotation with active learning |

## Open vs. Proprietary

**Mixed but heavily regulated**. Open datasets exist (TCIA, ChestX-ray14, BraTS for brain tumors) but they are small compared to the total medical data locked inside hospital systems. The real challenge is not annotation tools — it is legal and logistical access to the data.

## The Stalwart

**TCIA (The Cancer Imaging Archive)**. An NIH-funded project that hosts over 30 million de-identified medical images across dozens of cancer types, with standardized formats, rich metadata, and completely free access. It enables researchers in developing countries to train diagnostic models without partnerships with billion-dollar hospital systems. A government project done right.

## TBD

- Federated learning in healthcare: training models without sharing patient data across institutions
- FDA validation requirements for training data: what does "ground truth" mean when experts disagree?
- AI-assisted annotation in medicine: can models pre-label scans for radiologist review?
- Multi-modal medical AI: combining imaging, genomics, and clinical notes
- Pathology at scale: annotating whole-slide images that are 100,000 x 100,000 pixels

---

*Back to [overview](00-overview.md)*
