---
upstream: https://github.com/metallb/metallb
last_updated: 2026-08-16
---

# metallb — API reference

MetalLB registers nine custom resource kinds under API group `metallb.io`: eight in **`metallb.io/v1beta1`** and **`metallb.io/v1beta2`**, which exists only for `BGPPeer` (its storage version since v0.14.9; the `v1beta1` representation is still served but deprecated). The full generated reference is maintained upstream: [metallb.universe.tf/apis/](https://metallb.universe.tf/apis/); the Go type definitions live in [`api/`](https://github.com/metallb/metallb/tree/main/api).

| Kind | API version | Purpose | Upstream API docs |
| ---- | ----------- | ------- | ----------------- |
| `IPAddressPool` | `metallb.io/v1beta1` | A pool of IP addresses MetalLB can allocate to LoadBalancer services: address ranges/CIDRs, `autoAssign`, `avoidBuggyIPs`, and `serviceAllocation` (priority + namespace/service selectors). Status carries available/assigned counters per IP family (since v0.15.0). | [docs](https://metallb.universe.tf/apis/#ipaddresspool) |
| `L2Advertisement` | `metallb.io/v1beta1` | Advertises the IPs of the selected pools via L2 (ARP/NDP): pool names or selectors, `nodeSelectors`, `interfaces`, and `serviceSelectors`. | [docs](https://metallb.universe.tf/apis/#l2advertisement) |
| `BGPPeer` | `metallb.io/v1beta2` | A BGP session to a router: `myASN` plus `peerASN`/`dynamicASN` (`internal`/`local`), `peerAddress` or `interface` (unnumbered peering), timers, `routerID`, `peerPort`, `nodeSelectors`, `password`/`passwordSecret`, `bfdProfile`, `enableGracefulRestart`, `ebgpMultiHop`, `vrf`, `localASN` (since v0.16.0), `dualStackAddressFamily`. | [docs](https://metallb.universe.tf/apis/#bgppeer) |
| `BGPAdvertisement` | `metallb.io/v1beta1` | Advertises pool IPs via BGP with optional attributes: `aggregationLength`/`aggregationLengthV6` (roll-up prefixes), `localPref`, `communities`, `nodeSelectors`, `peers` (limit to specific BGPPeers), `serviceSelectors`. | [docs](https://metallb.universe.tf/apis/#bgpadvertisement) |
| `BFDProfile` | `metallb.io/v1beta1` | BFD session parameters (transmit/receive intervals, `detectMultiplier`, `echoMode`, `passiveMode`, `minimumTtl`) applied to BGPPeers via `spec.bfdProfile`. | [docs](https://metallb.universe.tf/apis/#bfdprofile) |
| `Community` | `metallb.io/v1beta1` | Named aliases for BGP community values (standard `1234:1234` or `large:...`) referenceable from `BGPAdvertisement.spec.communities`. | [docs](https://metallb.universe.tf/apis/#community) |
| `ServiceBGPStatus` | `metallb.io/v1beta1` | Read-only per-node status: the BGP peers each LoadBalancer service is configured to be advertised to (since v0.15.0). | [docs](https://metallb.universe.tf/apis/#servicebgpstatus) |
| `ServiceL2Status` | `metallb.io/v1beta1` | Read-only per-node status for L2 mode: the node receiving directed traffic for each service and the interface(s) (since v0.14.6). | [docs](https://metallb.universe.tf/apis/#servicel2status) |
| `ConfigurationState` | `metallb.io/v1beta1` | Status-only CRD reporting configuration validation results per component — labeled `metallb.io/component-type` (`controller`/`speaker`) and `metallb.io/node-name` — with `result`, `errorSummary`, and `conditions` (since v0.15.3). | [docs](https://metallb.universe.tf/apis/#configurationstate) |

Notes:

- None of the kinds has a short name; use the full kind name in manifests.
- `BGPPeer` objects created as `metallb.io/v1beta1` are stored as `v1beta2` (conversion webhook); prefer `v1beta2` for new resources.
- The pre-CRD configuration API (`address.metallb.io` pool and `bgpeer.metallb.io` kinds) was removed; see [Migration to CRDs](https://metallb.universe.tf/configuration/migration_to_crds/).
- Service-side settings (`loadBalancerIP`, pool selection via annotations, `prefer-dual-stack` `IPFamilyPolicy`) are ordinary fields/annotations on the `Service`, not MetalLB kinds.
- Field-level documentation is intentionally not duplicated here; follow the per-kind upstream links above.
