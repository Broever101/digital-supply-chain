# 2. Computer Vision Annotation

## What Goes In

Digital images and video frames: photographs, medical scans, satellite imagery, drone footage, security camera feeds, product photos, and synthetic renders.

## What Comes Out

Labeled visual data where every object, region, or pixel has been categorized, bounded, or segmented so that models can recognize, localize, and understand visual content.

## Annotation Types

### Image Classification
Assigning a single label to an entire image.
- **Example**: Photo of a dog → label: `dog`.
- **Complex variant**: Multi-label classification (dog + beach + sunset).

### Object Detection (Bounding Boxes)
Drawing rectangles around objects and labeling each one.
- **Example**: Street scene → boxes around cars, pedestrians, traffic lights, bicycles.
- **Metrics**: Intersection over Union (IoU) measures box accuracy.

### Semantic / Instance Segmentation
Labeling every pixel in an image. Semantic segmentation groups all pixels of the same class together. Instance segmentation separates individual objects of the same class.
- **Example**: A photo of a crowd → semantic: all people are one color; instance: each person gets a different color.

### Keypoint / Landmark Annotation
Marking specific points on objects: joints on a human body, facial landmarks, vehicle corners.
- **Example**: Pose estimation: 17 keypoints per person (nose, eyes, shoulders, elbows, wrists, hips, knees, ankles).

### Polygon Annotation
Drawing freehand shapes around irregular objects that bounding boxes cannot capture well.
- **Example**: A tree, a puddle, a cloud, or a cell under a microscope.

### Video Annotation (Tracking Across Frames)
Following objects as they move through time. Requires maintaining identity across frames.
- **Example**: Tracking a specific pedestrian from frame 1 to frame 300 as they cross a street.

## Key Players

| Name | URL | Role |
|------|-----|------|
| Scale AI | https://scale.com | Bounding boxes, segmentation, video at massive scale |
| SuperAnnotate | https://www.superannotate.com | Pixel-accurate tools, medical and AV focus |
| Labelbox | https://labelbox.com | Enterprise vision workflows, model-assisted labeling |
| V7 Labs | https://www.v7labs.com | AI-assisted polygon segmentation |
| CVAT (Intel) | https://www.cvat.ai | Open-source computer vision annotation standard |
| iMerit | https://imerit.net | Expert annotators for medical imaging and autonomous vehicles |
| Hive | https://thehive.ai | Pre-labeled image datasets for content moderation |
| Encord | https://encord.com | Active learning for vision, quality analytics |

## Open vs. Proprietary

**Mixed**. Academic datasets (ImageNet, COCO, PASCAL VOC) are open and foundational. Commercial annotation for enterprise and medical use is almost entirely proprietary. Open-source tools (CVAT, Label Studio) democratize access but still require human labor.

## The Stalwart

**CVAT (Computer Vision Annotation Tool)**. Originally built by Intel's open-source team, it became the default annotation tool for computer vision researchers and startups worldwide. Supports bounding boxes, segmentation, video tracking, and attribute annotation — all free, self-hosted, and extensible. A single research lab can label a million images without paying enterprise platform fees.

## TBD

- Panoptic segmentation: combining semantic and instance segmentation into one unified task
- Video annotation at scale: the challenge of tracking hundreds of objects across thousands of frames
- Medical imaging annotation: DICOM standards, 3D volume labeling, radiologist workflows
- Synthetic data for vision: generating labeled images from 3D engines to reduce human annotation needs
- Active learning in vision: models identifying which unlabeled images are most valuable to annotate next

---

*Back to [overview](00-overview.md)*
