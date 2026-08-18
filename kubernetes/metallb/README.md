---
upstream: https://github.com/metallb/metallb
last_updated: 2026-08-16
---

# metallb

[MetalLB](https://metallb.universe.tf/) provides the `LoadBalancer` Service type for Kubernetes installations where no cloud-provider load balancer exists (bare metal, on-premises, air-gapped, and homelab clusters). Instead of calling a cloud API, it allocates IPs from user-defined pools and advertises them to the network — either over L2 (ARP/NDP, no router configuration required) or BGP (routers announce the service IPs) — and routes traffic to the right node.

- Upstream repository: https://github.com/metallb/metallb
- Documentation: [metallb.universe.tf](https://metallb.universe.tf/) (latest version); canonical API reference: [metallb.universe.tf/apis/](https://metallb.universe.tf/apis/)
- License: Apache-2.0
- API group/version: `metallb.io/v1beta1` (BFDProfile, BGPAdvertisement, Community, ConfigurationState, IPAddressPool, L2Advertisement, ServiceBGPStatus, ServiceL2Status) and `metallb.io/v1beta2` (BGPPeer)

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
