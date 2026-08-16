---
upstream: https://github.com/metallb/metallb
last_updated: 2026-08-16
---

# metallb — features

Key feature areas, each linked to the upstream documentation covering it. The [metallb.universe.tf](https://metallb.universe.tf/) documentation site is authoritative; the [CRD API reference](https://metallb.universe.tf/apis/) details every field of the configuration kinds.

## Load-balancing modes

- **L2 (layer 2) mode**: advertises LoadBalancer service IPs via ARP/NDP, replacing the built-in `kube-proxy` mode for bare-metal networks — no external routing changes required. [concepts/layer2](https://metallb.universe.tf/concepts/layer2/), [L2 configuration](https://metallb.universe.tf/configuration/_advanced_l2_configuration/)
- **BGP mode**: advertises service IPs to BGP routers so traffic is routed to the right node; supports multi-prefix advertisement, unnumbered peering, and per-neighbor customization. [concepts/bgp](https://metallb.universe.tf/concepts/bgp/), [BGP configuration](https://metallb.universe.tf/configuration/_advanced_bgp_configuration/)
- **BGP backends**: the native Go BGP implementation (EOL), FRR in-pod, and **FRR-K8s** (external operator running FRR daemons in per-node Pods) — FRR-K8s is the default since v0.16.0 and legacy FRR mode is deprecated. [BGP configuration](https://metallb.universe.tf/configuration/_advanced_bgp_configuration/)

## IP address management

- **`IPAddressPool`**: named pools of static IPs allocated to LoadBalancer services — CIDRs and ranges, `autoAssign`/`ipFamily`, `avoidBuggyIPs` (first/last host exclusions), and per-pool allocation **priority** with namespace/service scoping (`serviceAllocation`). [advanced pool configuration](https://metallb.universe.tf/configuration/_advanced_ipaddresspool_configuration/)
- **Per-pool status** (v0.15.0+): available/assigned counters per IP family. [API reference](https://metallb.universe.tf/apis/#ipaddresspool)

## Advanced BGP (v0.14.9+ / v0.15.0 / v0.16.0)

- **`dynamicASN`**: discover the peer's ASN automatically (`internal`/`external`) instead of hard-coding it. [API reference](https://metallb.universe.tf/apis/#bgppeer)
- **Unnumbered peering** (v0.15.0): `BGPPeer.spec.interface` peers over a node interface without a peering IP. [API reference](https://metallb.universe.tf/apis/#bgppeer)
- **`localASN`** (v0.16.0): per-session AS override (BGP local-as, `no-prepend replace-as`). [Release notes](https://metallb.universe.tf/release-notes/#version-0-16-0)
- **`dualStackAddressFamily`** (v0.15.0): dual-stack sessions with per-neighbor v4/v6 address-family selection (replaces deprecated `disableMP`). [API reference](https://metallb.universe.tf/apis/#bgppeer)
- **BFD**: `BFDProfile` kinds with echo/passive modes wired to peers for sub-second failure detection. [API reference](https://metallb.universe.tf/apis/#bfdprofile)
- **Communities and route attributes**: `Community` alias kinds, `localPref`, and aggregation-prefix (`aggregationLength`/`aggregationLengthV6`) advertisement via `BGPAdvertisement`. [API reference](https://metallb.universe.tf/apis/#bgpadvertisement)

## Service-level configuration

- **`loadBalancerIP` / `loadBalancer IPs` annotations**: pin specific service IPs from a pool (`metallb.io/loadBalancerIP`; prefix migrated from `metallb.universe.tf/` in v0.14.9). [Usage](https://metallb.universe.tf/usage/)
- **`prefer-dual-stack` IPFamilyPolicy** (v0.14.9+): dual-stack LoadBalancer services. [Usage](https://metallb.universe.tf/usage/)
- **Advertisement selectors** (v0.16.0): `serviceSelectors` on `BGPAdvertisement`/`L2Advertisement` to limit which Services are advertised. [Release notes](https://metallb.universe.tf/release-notes/#version-0-16-0)

## Observability and status

- **`ServiceL2Status` / `ServiceBGPStatus`** CRDs: read-only per-node view of which node receives L2 traffic (and its interfaces) / which BGP peers a service is advertised to. [API reference](https://metallb.universe.tf/apis/#servicel2status)
- **`ConfigurationState`** CRD (v0.15.3): controller/speaker configuration validation results in-cluster (`result`, `errorSummary`, `conditions`). [API reference](https://metallb.universe.tf/apis/#configurationstate)- **Prometheus metrics**: per-node `metallb_node_*`, controller `metallb_allocator_*`/prometheus exporter; HTTPS-only with native RBAC since v0.16.0, optional `ServiceMonitor`/`PodMonitor` annotations. [Prometheus monitoring](https://metallb.universe.tf/prometheus-metrics/)
- **Troubleshooting guide**: speaker/controller log interpretation, BGP session and advertisement debugging. [troubleshooting](https://metallb.universe.tf/troubleshooting/)

## Installation and compatibility

- **Helm chart** (`metallb` chart from `https://metallb.github.io/metallb`, also on quay.io since v0.15.2) and **all-in-one manifest / kustomize** for cluster installation; `controllerConfig` CRD for the native TLS webhook configuration. [installation](https://metallb.universe.tf/installation/)
- **Cloud provider notes**, plus dedicated guidance for networking add-ons that interact with ARP/routing: [clouds](https://metallb.universe.tf/installation/clouds/), [network add-ons](https://metallb.universe.tf/installation/network-addons/), [Calico](https://metallb.universe.tf/configuration/calico/), [K3s](https://metallb.universe.tf/configuration/k3s/), [kube-router](https://metallb.universe.tf/configuration/kube-router/)
- **CRD-based configuration migration**: the old `address.metallb.io`/`bgpeer.metallb.io` API is removed; [migration guide](https://metallb.universe.tf/configuration/migration_to_crds/).

## Security

- TLS for the admission webhook and metrics endpoints, `--tls-min-version`/`--tls-cipher-suites` flags, RBAC on the metrics endpoint, and a default NetworkPolicy (chart/manifests, v0.15.3+). [installation](https://metallb.universe.tf/installation/), [Prometheus monitoring](https://metallb.universe.tf/prometheus-metrics/)
- Distroless images (v0.14.6+) and [CVE-2025-22874](https://nvd.nist.gov/vuln/detail/CVE-2025-22874) fixed in v0.15.3. [Release notes](https://metallb.universe.tf/release-notes/#version-0-15-3)
