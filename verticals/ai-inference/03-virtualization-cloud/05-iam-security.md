# IAM, Security, and Multi-Tenancy

## What Goes In

A pile of GPUs, a network, and a bunch of users who should not be able to see each other.

## What Comes Out

Isolated tenants, attested hardware, secrets management, and (for sovereign workloads) provable data residency.

Sovereign cloud security is not the same as enterprise security. The threat model is different: the operator may be a third party (a national telecom), the workload owner may be a government, and the hardware may have been procured through channels that are not fully trusted. The primitives covered here are the ones that make this tractable.

## Secrets Management

- **HashiCorp Vault** — the de facto standard for secrets management. Key/value, dynamic secrets, transit encryption, lease management. **Note**: IBM acquired HashiCorp in 2024; the open-source community has forked Vault into **OpenBao** under the Linux Foundation. Worth tracking for sovereign deployments that want a fully open-source dependency tree.
- **CyberArk Conjur** — enterprise secrets, RBAC focus.
- **AWS Secrets Manager / GCP Secret Manager / Azure Key Vault** — the hyperscaler defaults.

## Identity and Access Management

- **Keycloak** — open-source Identity and Access Management (IdP), SAML, OIDC. The sovereign-cloud default. Red Hat sponsors.
- **Authelia**, **Authentik** — lighter-weight open-source IdPs.
- **Okta**, **Ping**, **Microsoft Entra ID** — commercial alternatives.

## Kubernetes-Native Security

- **Kubernetes RBAC + NetworkPolicy** — the baseline. Namespaces + RBAC for coarse-grained isolation; NetworkPolicy for traffic control.
- **Cilium** (Cisco/Isovalent) — eBPF-based CNI and security layer. Becoming the default AI-cluster CNI because it handles high-throughput east-west traffic well. Layer 7 awareness.
- **Calico** — the other major CNI; widely deployed.
- **Istio, Linkerd, Consul** — service meshes. Heavyweight; mostly used for enterprise microservices, less common for AI workloads.

## Confidential Computing — The Strongest Isolation

For workloads where the operator cannot be trusted with plaintext data (regulated industries, government, defense), **Confidential Computing** encrypts data in use. Covered in detail in [GPU sharing](02-gpu-sharing.md).

The core point: NVIDIA Confidential Computing on Hopper (extending with Vera Rubin) puts the trust boundary at the GPU itself. The tenant's code and model weights are protected even from the host operator. Adopters: Google Cloud, Azure, Anthropic, xAI, Apple Private Cloud Compute, Anjuna, Edgeless, Fortanix, Opaque, EQTY Lab, Red Hat.

**Apple Private Cloud Compute** is the canonical case: Apple runs its Foundation Models on NVIDIA CC on Google Cloud — a "corporate-sovereign without owning a datacenter" pattern. Apple gets the cost benefits of public cloud while keeping user data cryptographically isolated from Google.

## Attestation

How do you prove the hardware you're running on is actually doing what it claims? Attestation.

- **NVIDIA Attestation SDK** — verify that a GPU is running in CC mode with the expected firmware.
- **Veraison** — open-source verification framework for confidential computing evidence.
- **nvtrust** (Apache-2.0, `github.com/NVIDIA/nvtrust`) — guest-side attestation SDK and host tools to configure GPU CC mode, with sample KVM scripts for launching Confidential VMs.

## Supply Chain Integrity

For sovereign clouds, the supply chain is part of the threat model.

- **SBOMs (Software Bills of Materials)** — **Syft** (generate), **Grype** (scan for vulnerabilities), **Trivy** (combined). Now required for US federal procurement.
- **Sigstore** — cryptographic signing of container images and artifacts. cosign, rekor, fulcio.
- **SLSA** (Supply-chain Levels for Software Artifacts) — framework for hardening the supply chain.
- **TUF** (The Update Framework) — secure update systems.

## Air-Gapped Operations

Sovereign clouds often need to operate without internet connectivity. This is harder than it sounds.

- **Disconnected updates** — local mirrors of package repositories, OS image registries, container registries (Harbor).
- **No outbound calls** — many "open-source" tools phone home (telemetry, license checks). Audit before deploy.
- **Local time synchronization** — NTP servers, GPS clocks.
- **Local certificate authorities** — for internal TLS.

## The Reference Security Stack

For a sovereign cloud deployment:

- **Bare metal**: attested boot, signed OS images
- **Network**: Cilium CNI, NetworkPolicy enforcement, BlueField DPU at the tenant boundary
- **Identity**: Keycloak IdP, OIDC tokens
- **Secrets**: Vault (or OpenBao for fully-OSS)
- **Workload isolation**: NVIDIA Confidential Computing for sensitive inference
- **Supply chain**: SBOMs (Syft), image signing (cosign), admission control (Kyverno or OPA Gatekeeper)
- **Audit**: Loki for logs, audit events to immutable storage
- **Updates**: local Harbor registry, TUF-signed updates

## The Stalwart

**OpenBao** is the Stalwart of this layer. When IBM acquired HashiCorp in 2024 and HashiCorp shifted Vault to the BSL license, the open-source community forked Vault into OpenBao under the Linux Foundation. For sovereign deployments that need a fully open-source dependency tree, OpenBao is the answer. The fact that it exists at all is a credit to the OSS community's refusal to let a critical primitive fall under commercial control.

The other Stalwart: **EQTY Lab**, the only serious player in model-weight attestation — cryptographic proof of which model weights are actually running on which hardware. As AI regulation matures, this primitive becomes first-order important, and almost nobody is building it.

---

*Next: [06-observability-billing](06-observability-billing.md)*
