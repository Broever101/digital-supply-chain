# 3. Synthetic / Simulated Data

## What Goes In

Real-world data that is too expensive, too slow, too private, or too dangerous to collect at scale. Think: rare diseases, car crashes, financial fraud, or autonomous vehicle edge cases.

## What Comes Out

Artificial datasets that statistically mirror real data without containing actual sensitive records. Used for training, testing edge cases, and augmenting limited real datasets.

## Key Players

| Name | URL | Role |
|------|-----|------|
| Gretel | https://gretel.ai | Synthetic tabular and text data with privacy guarantees |
| Mostly AI | https://mostly.ai | Synthetic data platform for enterprise |
| Synthesis AI | https://synthesis.ai | Synthetic human faces and poses for computer vision |
| Parallel Domain | https://paralleldomain.com | Synthetic 3D environments for autonomous vehicles |
| Hazy | https://hazy.com | Synthetic data for compliance and privacy |
| NVIDIA Omniverse | https://www.nvidia.com/omniverse | Physics-based simulation for robotics and manufacturing |
| Unity Simulation Pro | https://unity.com/products/simulation-pro | 3D simulation for training autonomous systems |

## Open vs. Proprietary

The **generation platforms are proprietary**, but many **datasets produced are shared openly** for research. The underlying simulation engines (Unity, Unreal) are widely accessible.

## The Stalwart

**Gretel**. A small team built a synthetic data engine that lets healthcare and finance companies train models without ever touching a real patient's record. The interface is simple, the privacy guarantees are mathematically rigorous, and it runs in the cloud or on-premise.

## TBD

- Differential privacy techniques and their tradeoffs
- When synthetic data fails: distribution drift, mode collapse
- Regulatory acceptance: can synthetic data replace real data for compliance?
- The rise of "AI training AI" — using LLMs to generate training data for smaller models

---

*Back to [overview](00-overview.md)*
