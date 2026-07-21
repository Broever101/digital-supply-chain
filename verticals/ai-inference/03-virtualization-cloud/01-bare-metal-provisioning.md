# Bare-Metal Provisioning and Rack Architecture

## What Goes In

A building. Megawatts of power. A floor that can hold 1,000+ kilograms per rack. A network drop.

## What Comes Out

A rack of imaged, networked, GPU-equipped servers ready to join a Kubernetes or Slurm cluster.

This is the foundation of the foundation. Get this wrong and nothing above it works.

## Site Selection

The four variables that determine where a sovereign AI datacenter can go:

- **Power cost and availability** — AI racks draw 40–120 kW each, vs 5–10 kW for a traditional datacenter rack. A 100 MW substation feed is now a starting point. Iceland, Norway, Québec, Texas, the US Pacific Northwest, Ireland, Malaysia, and the UAE are popular because power is cheap, abundant, or both.
- **Climate** — Free cooling (outside-air economization) works below ~25°C wet-bulb. Above that, you pay for mechanical cooling, which destroys PUE. Northern climates win.
- **Fiber and geography** — Inference workloads need low latency to users; training just needs to ship checkpoints. Sovereign workloads often optimize for *distance from a national border* rather than from users.
- **Regulation** — data residency laws, environmental permits, nuclear licensing (if you want SMRs), tax incentives.

**Power usage effectiveness (PUE)** benchmarks what fraction of total facility power goes to compute (vs cooling/overhead). Hyperscaler best-in-class is ~1.1; the industry average is ~1.55; legacy enterprise datacenters are 2.0+. Cooling is the dominant variable.

## Power Sources — The 2026 Strategic Battleground

The cheapest kilowatt-hour is the one you do not have to generate.

- **Grid power** — default. Subject to utility procurement, carbon intensity, and rate volatility.
- **Behind-the-meter (BTM) deals** — the datacenter is co-located with a power plant and buys direct. AWS → Talen Energy (March 2024, $650M for the Susquehanna nuclear campus) is the canonical example. **The November 2024 FERC ruling rejected the BTM cost-allocation structure**, chilling this market — future BTM deals have to be structured as pure PPAs.
- **PPAs (Power Purchase Agreements)** — Microsoft → Constellation / Three Mile Island Unit 1 (September 2024, 20-year PPA to restart TMI-1, ~$1.6B restart cost, target online 2028).
- **SMRs (Small Modular Reactors)** — Helion Energy (Sam Altman-backed fusion, $15.5B valuation June 2026, Microsoft PPA), Oklo, NuScale (Idaho project cancelled 2023), X-energy. Multiple MOUs but no commercial SMR operating in the US as of mid-2026.
- **Flare-gas / stranded energy** — Crusoe Energy repurposes flared natural gas from oil wells. Series D ~$686M in 2024 at ~$2.5B valuation. Subsidized by otherwise-wasted gas; long-term availability is policy-dependent since flaring is a regulatory target.

## Cooling

- **Air cooling** — viable to ~30–40 kW per rack. HGX H100 and H200 systems typically air-cooled. Above that, you need liquid.
- **Direct-to-chip liquid cooling (D2C)** — cold plates on the GPUs, coolant distribution units (CDUs) per rack. Required for Blackwell (B200 at 1,000W, GB200 NVL72 at ~120 kW/rack). The default for new builds in 2026.
- **Immersion cooling** — tanks of dielectric fluid (single-phase or two-phase). Some CoreWeave and Meta pilots. Higher capital cost but better PUE. Less mainstream than D2C.
- **Rear-door heat exchangers** — a retrofit option that closes the gap between air cooling and D2C. Useful for brownfield sites.

## Rack Architecture — NVIDIA's Platform Lineage

NVIDIA's datacenter platform family is now five layers deep:

- **HGX** — the baseboard reference design. 4-GPU and 8-GPU baseboards sold to OEMs (SuperMicro, Dell, HPE, Lenovo, Cisco, Gigabyte, Asus). The workhorse.
- **DGX** — NVIDIA's branded turnkey server built on HGX. Comes with DGX OS.
- **MGX** — modular reference architecture for partner systems. More flexibility, faster time-to-market for OEMs.
- **DSX** (2026) — the full "AI factory" platform. DSX Reference Design + DSX Sim + **DSX OS** + DSX MaxLPS + DSX Flex + DSX Exchange. Partners: IREN (5 GW), Jacobs, Phaidra, Schneider/EcoDataCenter, Emerald AI. This is NVIDIA's sovereign-cloud-in-a-box play.
- **NVL72** — rack-scale (72-GPU NVLink domain). Liquid-cooled. GB200 NVL72 shipping since February 2025, GB300 since July 2025. Roughly $3M per rack.

The progression is significant: HGX sells components, DGX sells a server, MGX sells a modular design, DSX sells a whole facility, NVL72 sells a rack. NVIDIA keeps moving up the value chain.

## OEM and ODM Options

- **Tier-1 OEMs** (NVIDIA partners): SuperMicro, Dell, HPE, Lenovo, Cisco. All build to HGX/MGX/DSX reference designs. Warranty, support, enterprise sales motion.
- **ODM-direct** (sell to hyperscalers and large neo-clouds): Foxconn, Wiwynn, Quanta, Wistron, Inventec. Cheaper at scale, less hand-holding.
- **Open Compute Project (OCP)** — Facebook-founded open hardware standardization effort. **OAI (Open Accelerator Infrastructure)** is the OCP effort to standardize accelerator modules outside NVIDIA's HGX monopoly. Worth tracking but not yet mainstream.

## Lead Times — The Hidden Story

In 2024, lead times for H100s were 36–52 weeks. In 2025, B200 lead times were 6–9 months. By mid-2026, NVIDIA's supply has caught up for H100/H200, but Blackwell remains allocation-constrained for non-hyperscaler buyers. MI300X is roughly 8–16 weeks. Huawei Ascend 910C lead time is opaque; Chinese customers report 3–6 months.

Sovereign buyers should plan procurement 12 months ahead of need.

## Bare-Metal Provisioning

Once the hardware arrives, you have to image it. The options:

- **MAAS (Metal-as-a-Service)** from Canonical — the de facto standard for bare-metal provisioning. IPMI/Redfish-based. Free / $30 / $50 per machine per year. Customers: T-Mobile, AT&T, Verizon, NTT, BT, Telefonica, Barclays, Sandia, Ubisoft, Roblox, EA.
- **Tinkerbell** — Linux Foundation, K8s-native bare-metal workflow. Components: Smee (DHCP/tftp), Tootles (iPXE), Hook (OS installer), Tink (workflow engine), Rufio (BMC controller), CAPT (Cluster API provider). The open-source purist's choice.
- **Digital Rebar** (RackN) — commercial alternative with multi-site management.
- **IronIO** and similar — older approaches, less common for AI workloads.

Underneath all of these is **IPMI / Redfish / BMC** — the out-of-band management standards that let you control a server before it has an OS. Every server has a BMC (Baseboard Management Controller); Redfish is the modern REST API standard that replaced IPMI.

## Operating System Choices

- **NVIDIA DGX OS** — Ubuntu-based, NVIDIA-optimized, ships with DGX systems. The "supported path."
- **Ubuntu Pro / Ubuntu Server** — the most common base for non-DGX deployments. Canonical's MAAS + Ubuntu Pro combination is the de facto sovereign-cloud stack.
- **Talos Linux** (Sidero Labs) — immutable, API-only Linux for Kubernetes. No SSH, no shell. Emerging as the default for sovereign and edge. The "modern" choice.
- **RHEL / Rocky / Mariner** — Red Hat ecosystem. Required if you're using OpenShift AI.
- **Flatcar Container Linux** — CoreOS descendant, immutable, container-first.

## Driver and CUDA Stack

The NVIDIA software stack on bare metal:

- **NVIDIA Open GPU Kernel Modules** — open-source kernel-side driver (since 2022). The userspace CUDA libraries remain closed.
- **CUDA** versions — Pin yours. CUDA 12.x is current as of 2026.
- **Container runtimes** — containerd (default), CRI-O (Red Hat). Docker is fine for dev.
- **NVIDIA Container Toolkit** — the bridge that lets containers see GPUs.
- **NVIDIA GPU Operator** — Kubernetes-native; manages driver install, device plugin, DCGM exporter, MIG configs.

## The Stalwart

**Talos Linux** is the Stalwart of this layer. Immutable, API-only, no SSH, no shell. You manage it through a Kubernetes-style API. Image-based updates — atomic, no package manager. Six-person company (Sidero Labs), but they are quietly becoming the default for sovereign and edge Kubernetes deployments. The conceptual move from "pet Linux server I SSH into" to "cattle Linux image I declare in YAML" is the same move Kubernetes made for applications.

---

*Next: [02-gpu-sharing](02-gpu-sharing.md)*
