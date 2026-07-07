# 5. Autonomous Vehicles

## What Goes In

Sensor data from vehicles operating in real-world traffic: camera feeds, LiDAR point clouds, radar returns, GPS tracks, and CAN bus telemetry.

## What Comes Out

Labeled perception datasets where every object, lane marking, traffic sign, and drivable surface has been identified and tracked across time and space.

## Why This Domain Is Different

Autonomous vehicle annotation is the most demanding annotation domain. It requires:
- **Multiple sensor modalities** synchronized in time (camera + LiDAR + radar)
- **3D spatial annotation** with centimeter-level accuracy
- **Temporal tracking** of objects across hundreds of frames
- **Safety-critical standards** where a missed pedestrian is catastrophic
- **Massive scale**: training a single AV model can require billions of labeled examples

## Annotation Tasks Specific to AVs

### Object Detection and Tracking
Identifying and following vehicles, pedestrians, cyclists, traffic signs, and traffic lights across video sequences.
- **Challenge**: Occlusions (a pedestrian hidden by a parked truck), varying lighting, weather conditions.

### Lane and Road Surface Annotation
Marking lane boundaries, crosswalks, stop lines, and drivable vs. non-drivable surfaces.
- **Challenge**: Faded lane markings, construction zones, unpaved roads.

### Behavior and Intention Prediction
Labeling what actors are likely to do next: a pedestrian about to cross, a car about to change lanes.
- **Challenge**: Subjective judgment; requires understanding of traffic dynamics.

### HD Map Alignment
Aligning sensor data with high-definition maps that contain centimeter-accurate road geometry.
- **Challenge**: Map drift, GPS inaccuracies, and real-world changes vs. static maps.

## Key Players

| Name | URL | Role |
|------|-----|------|
| Scale AI | https://scale.com | Dominant AV annotation provider; Waymo, Cruise, Argo partner |
| iMerit | https://imerit.net | Expert AV annotation with trained workforce |
| SuperAnnotate | https://www.superannotate.com | AV-focused pixel-accurate tools |
| Parallel Domain | https://paralleldomain.com | Synthetic AV data with auto-labeling |
| nuScenes / Motional | https://www.nuscenes.org | Open AV dataset for research |
| Waymo Open Dataset | https://waymo.com/open | Waymo's open research dataset |
| Lyft Level 5 Dataset | https://level-5.lyft.com | Open AV dataset from Lyft's self-driving program |

## Open vs. Proprietary

**Mostly proprietary**. AV companies treat their data as their most valuable asset. The few open datasets (nuScenes, Waymo Open, Lyft L5) are carefully curated subsets released for research, not the full proprietary training sets.

## The Stalwart

**nuScenes**. Released by an AV company (Motional) that understood the field would advance faster if researchers had access to high-quality data. It includes full sensor suites, 3D annotations, and tracking across 1,000 scenes. A rare case of a competitive company open-sourcing data for the greater good of the industry.

## TBD

- The shift from rule-based to end-to-end learning: does annotation change when models learn directly from video?
- Weather and corner cases: annotating snow, fog, night scenes, and rare events
- Sim-to-real gap: how well do synthetic labels transfer to real-world driving?
- Regulatory requirements: what annotation standards will governments mandate for AV safety validation?

---

*Back to [overview](00-overview.md)*
