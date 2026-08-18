---
upstream: https://github.com/metallb/metallb
last_updated: 2026-08-16
---

# metallb — releases

Latest 10 official releases, newest first. Check the ⚠️ entries before upgrading. Detailed notes are maintained at [metallb.universe.tf/release-notes/](https://metallb.universe.tf/release-notes/); for v0.16.1 and v0.15.1 there is no GitHub Release object (tag-only releases), so the documentation-site anchors are the canonical links.

## 0.16.1 — 2026-05-27

[Release notes](https://metallb.universe.tf/release-notes/#version-0-16-1)

- Bug-fix release on top of 0.16.0 — [Helm chart rendering fix](https://github.com/metallb/metallb/pull/3058).
- `BGPPeer` v1beta2 `localASN` field now declared `int64` (was rendered without a format) — [metallb#3054](https://github.com/metallb/metallb/pull/3054).
- Controller health probes fixed and the health bind address made configurable (defaults to all interfaces) — [metallb#3062](https://github.com/metallb/metallb/pull/3062).
- Prometheus scrape annotations (`PodMonitor`/`ServiceMonitor` templates) now emit `scheme: https` to match the HTTPS-only metrics endpoint introduced in 0.16.0 — [metallb#3055](https://github.com/metallb/metallb/pull/3055).

## 0.16.0 — 2026-05-20

[Release page](https://github.com/metallb/metallb/releases/tag/v0.16.0)

- ⚠️ **FRR-K8s is now the default BGP backend**; the legacy FRR mode is deprecated and scheduled for removal (the native Go BGP implementation is EOL). Existing FRR installations should be moved to FRR-K8s.
- ⚠️ **Metrics endpoint moved behind TLS**: kube-rbac-proxy was replaced by native TLS + RBAC on the controller — metrics are served over HTTPS only and the old plain-HTTP scrape target no longer exists. Update Prometheus `ServiceMonitor`/scrape configs (see also the 0.16.1 annotation fix).
- **`BGPPeer.spec.localASN`**: advertise a different AS number to a peer per session (BGP "local-as" — `no-prepend replace-as`); supported by FRR-based backends only. Not supported in the native BGP mode.
- **Configurable BGP debounce timeout** to tune how long advertised-prefix changes are debounced before being pushed to the BGP daemons.
- **`serviceSelectors` on advertisements**: `BGPAdvertisement` and `L2Advertisement` can now limit the set of Services they advertise, not only the pools.
- Memberlist (speaker cluster membership) configuration exposed; Helm chart now also published to quay.io.

## 0.15.3 — 2025-12-04

[Release page](https://github.com/metallb/metallb/releases/tag/v0.15.3)

- **`ConfigurationState` CRD**: status objects reporting controller/speaker configuration validation results (`result`, `errorSummary`, `conditions`) — the first first-class, in-cluster surface for "why is my config rejected".
- **NetworkPolicy** shipped in the Helm chart and all-in-one manifests (default-allow MetalLB traffic).
- Upgraded FRR to 10.4.1 and frr-k8s to 0.0.21.
- Security hardening: TLS min-version flags (`--tls-min-version`, `--tls-cipher-suites`) on the controller/webhook; fixed [CVE-2025-22874](https://nvd.nist.gov/vuln/detail/CVE-2025-22874).
- Optional resource requests/limits for the frr-k8s init container.

## 0.15.2 — 2025-06-04

[Release page](https://github.com/metallb/metallb/releases/tag/v0.15.2)

- Bug-fix release; Helm chart additionally published to quay.io (`quay.io/metallb/charts`).

## 0.15.1 — 2025-06-04

[Release notes](https://metallb.universe.tf/release-notes/#version-0-15-1)

- ⚠️ **Reverted the v0.15.0 behaviour** of rejecting `IPAddressPool`s whose addresses overlap the cluster's node CIDRs — that validation was causing false positives on networks where the pool legitimately shares space with node addresses. Pools overlapping node ranges are accepted again.

## 0.15.0 — 2025-06-03

[Release page](https://github.com/metallb/metallb/releases/tag/v0.15.0)

- **`IPAddressPool` status**: per-pool counters of available/assigned IPv4 and IPv6 addresses (first status on this kind).
- **`ServiceBGPStatus` CRD**: read-only per-node status showing the BGP peers each LoadBalancer service is advertised to.
- **Unnumbered BGP peering**: `BGPPeer.spec.interface` establishes peering over a node interface without a peering IP.
- ⚠️ **`BGPPeer.spec.disableMP` deprecated** in favor of `dualStackAddressFamily`; the daemon now defaults to v4-over-v4 / v6-over-v6 address-family selection per neighbor.
- `dualStackAddressFamily` per neighbor: advertise/receive IPv4 prefixes over IPv6 sessions (or vice versa).
- Rejected `IPAddressPool`s overlapping node CIDRs — **reverted in 0.15.1** (see above).

## 0.14.9 — 2024-12-17

[Release page](https://github.com/metallb/metallb/releases/tag/v0.14.9)

- ⚠️ **Storage version of `BGPPeer` moved to `metallb.io/v1beta2`** (v1beta1 remains served, deprecated).
- ⚠️ **Annotation prefix changed** from `metallb.universe.tf/` to `metallb.io/` for Service annotations (e.g. `metallb.io/loadBalancerIP`, `metallb.io/ip-family-select-policy`). Old annotations stop being honored — update manifests.
- **`BGPPeer.spec.dynamicASN`**: detect the neighbor’s ASN automatically — `internal` (reject if different from `myASN`) or `external` (reject if equal) — instead of hard-coding `peerASN`.
- `prefer-dual-stack` `IPFamilyPolicy` support for dual-stack LoadBalancer services.
- Prometheus alert rules now carry proper `severity` levels.

## 0.14.8 — 2024-07-23

[Release page](https://github.com/metallb/metallb/releases/tag/v0.14.8)

- Upgraded frr-k8s to 0.0.14.
- Fixed the all-in-one and kustomize manifests, which were broken by the 0.14.7 release process — 0.14.8 is the supported entry point of the 0.14.x line.

## 0.14.7 — 2024-07-16

[Release page](https://github.com/metallb/metallb/releases/tag/v0.14.7)

- Fixed a double-validation panic on immutable fields of the `ServiceL2Status` CRD (controllers crash-looped when the CRD was present).

## 0.14.6 — 2024-07-16

[Release page](https://github.com/metallb/metallb/releases/tag/v0.14.6)

- **`ServiceL2Status` CRD**: per-node status of L2 traffic (which node receives directed traffic for each service and on which interfaces).
- **Graceful restart** in FRR mode: speaker BGP sessions survive controller restarts.
- **External frr-k8s instances**: run the frr-k8s operator outside the MetalLB deployment and point at it.
- **Distroless images** for the controller and speaker.
- Fixed BFD session flapping; various frr-k8s upgrades.
