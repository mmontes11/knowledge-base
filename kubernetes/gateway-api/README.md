---
upstream: https://github.com/kubernetes-sigs/gateway-api
last_updated: 2026-08-23
---

# gateway-api

[Gateway API](https://gateway-api.sigs.k8s.io/) is the Kubernetes API specification for service traffic management: a set of custom resources under the `gateway.networking.k8s.io` group that model the traffic pipeline (`GatewayClass` → `Gateway` → route) so L4/L7 routing, TLS termination, and policy can be declared independently of the implementation (ingress controllers, load balancers, service meshes). Features are released on two channels — **Standard** (GA, backward-compatible) and **Experimental** (pre-graduation) — and implementations are verified by a [conformance suite](https://gateway-api.sigs.k8s.io/docs/concepts/conformance/) that exposes supported features in `GatewayClass` status.

- Upstream repository: https://github.com/kubernetes-sigs/gateway-api
- Documentation: https://gateway-api.sigs.k8s.io/ (site was migrated from MkDocs to Docsy in v1.6.0; old `/reference/spec/` URLs now 404 — per-kind pages live under `/reference/api-types/` and the full spec per release under `/reference/api-spec/<version>/spec/`)
- License: Apache-2.0

## Local deployment (this workspace)

- Installed by `mmontes11/k8s-infrastructure` via Flux into namespace `networking`: `infrastructure/gateway-api/kustomization.yaml` pulls the **experimental** channel CRDs from upstream (`https://github.com/kubernetes-sigs/gateway-api/config/crd/experimental?ref=v1.5.1`).
- Verified in-cluster on 2026-08-23: CRDs carry the annotations `gateway.networking.k8s.io/bundle-version: v1.5.1` and `gateway.networking.k8s.io/channel: experimental` (e.g. `gateways.gateway.networking.k8s.io`).
- Implementation is Traefik: `GatewayClass` `traefik-external` and `traefik-internal` (controller name `traefik.io/gateway-controller`) are accepted in the cluster, with `Gateway`, `HTTPRoute`, and `TCPRoute` objects throughout the infrastructure repository (e.g. `infrastructure/traefik/{external,internal}-gateway.yaml`, `infrastructure/buildkit/*-tcproute.yaml`).
- Upgrading the bundle from v1.5.x to v1.6.x is an API graduation event: `TCPRoute`/`UDPRoute` move to `v1` (see [releases](releases.md)) and the v1.6 CRDs stop serving `v1alpha2` `TLSRoute`. Traefik's Gateway API provider supports spec v1.6.1 only with the experimental-channel CRDs installed, which this cluster already does.

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
