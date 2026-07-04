# 5. Sensor & IoT / Machine-Generated

## What Goes In

Physical signals from the real world: light bouncing off objects, radio waves reflecting off surfaces, temperature changes, vibration patterns, heartbeats, and GPS coordinates.

## What Comes Out

Time-series data streams, point clouds, video feeds, and telemetry logs that describe the state of a physical system over time.

## Key Players

| Name | URL | Role |
|------|-----|------|
| Waymo | https://waymo.com | Autonomous vehicle sensor data (internal, not sold) |
| Tesla | https://www.tesla.com | Fleet learning from millions of vehicles |
| Velodyne / Ouster | https://ouster.com | LiDAR sensor manufacturers |
| Samsara | https://www.samsara.com | Industrial IoT fleet and equipment monitoring |
| Particle | https://www.particle.io | IoT device connectivity platform |
| HERE Technologies | https://www.here.com | Location data from connected vehicles |
| Telit | https://www.telit.com | IoT modules and connectivity |

## Open vs. Proprietary

Almost entirely **proprietary**. Sensor data is locked inside vehicle manufacturers, industrial operators, and wearable companies. The few open datasets (KITTI, nuScenes) are research releases, not live feeds.

## The Stalwart

**nuScenes**. A open-source autonomous driving dataset from Motional (formerly Aptiv) that includes 1,000 scenes with 1.4 million camera images, 390,000 LiDAR sweeps, and 23 object classes. Released for free to the research community and became a standard benchmark. A large company giving away its crown jewels to push the industry forward.

## TBD

- Data ownership in connected vehicles: who owns the data, the driver or the manufacturer?
- Industrial IoT data silos and the challenge of cross-facility aggregation
- Edge computing: processing sensor data before it ever hits the cloud
- Federated learning for IoT: training models without centralizing raw data

---

*Back to [overview](00-overview.md)*
