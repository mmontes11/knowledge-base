---
upstream: https://github.com/kubernetes-sigs/gateway-api
last_updated: 2026-08-23
---

# gateway-api — API reference

All Kubernetes kinds live in the API group **`gateway.networking.k8s.io`** (plus an experimental extended group `gateway.networking.x-k8s.io`), namespaced, and are delivered as CRDs from one of two install channels: `config/crd/standard` or `config/crd/experimental` in the upstream repo (or the `*-install.yaml` release artifacts). Per-kind field-level documentation is on the [api-types pages](https://gateway-api.sigs.k8s.io/reference/api-types/); the complete versioned spec is published per release, e.g. [v1.5 spec](https://gateway-api.sigs.k8s.io/reference/api-spec/1.5/spec/) and [v1.6 spec](https://gateway-api.sigs.k8s.io/reference/api-spec/1.6/spec/).

## Standard channel (served by both channel installs)

| Kind | API version | Purpose | Upstream API docs |
| ---- | ----------- | ------- | ----------------- |
| `GatewayClass` | `v1` | Declares a class of `Gateway`s handled by a traffic manager (controller name, `parametersRef`, and the conformance-reported `supportedFeatures`/status). | [docs](https://gateway-api.sigs.k8s.io/reference/api-types/gatewayclass/) |
| `Gateway` | `v1` | An instance of service-traffic infrastructure: binds Listeners (protocol/port/hostname/TLS, `allowedRoutes`) to addresses; carries infrastructure labels/annotations and `parametersRef`. | [docs](https://gateway-api.sigs.k8s.io/reference/api-types/gateway/) |
| `HTTPRoute` | `v1` | L7 HTTP routing: hostnames, path/header/query/method matching, filters (redirect/rewrite, header modifier, mirror, CORS, retries, timeouts, `ExtensionRef`), weighted `backendRefs`. | [docs](https://gateway-api.sigs.k8s.io/reference/api-types/httproute/) |
| `GRPCRoute` | `v1` | gRPC (HTTP/2) routing: service/method matching, mirrors, timeouts, backend refs. GA since v1.1. | [docs](https://gateway-api.sigs.k8s.io/reference/api-types/grpcroute/) |
| `ReferenceGrant` | `v1` (v1beta1 still served in v1.5.x) | Opt-in for cross-namespace object references (e.g. a cert `Secret` used by a `Gateway` in another namespace), the security boundary for cross-namespace `parentRef`/`certificateRefs`/backend refs. | [docs](https://gateway-api.sigs.k8s.io/reference/api-types/referencegrant/) |
| `BackendTLSPolicy` | `v1` | TLS policy for outbound (gateway → backend) connections: min version/ciphers, SNI/hostname, client certificate (mTLS), SAN validation; targets `Gateway`/`Service`/backend refs (single `targetRef` restriction in v1.4.x). GA since v1.4. | [docs](https://gateway-api.sigs.k8s.io/reference/api-types/policy/backendtlspolicy/) |
| `TLSRoute` | `v1` (v1alpha2/v1alpha3 still served in v1.5.x) | L4 TLS (passthrough) routing by SNI hostname toward backends. Standard `v1` since v1.5; up to 1024 hostnames/rules since v1.6. | [docs](https://gateway-api.sigs.k8s.io/reference/api-types/tlsroute/) |
| `ListenerSet` | `v1` | Decouples Listeners from the `Gateway` object: a named set of listeners that attaches to a `Gateway`'s `allowedListeners` scope, enabling multi-tenant/per-service listener management. Standard since v1.5. | [docs](https://gateway-api.sigs.k8s.io/reference/api-types/listenerset/) |

## Experimental channel only (as of v1.5.1; this workspace installs the experimental CRDs)

| Kind | API group/version | Purpose | Upstream API docs |
| ---- | ----------------- | ------- | ----------------- |
| `TCPRoute` | `gateway.networking.k8s.io/v1alpha2` | Raw L4 TCP routing (weighted routing, SNI-based TCP matches where supported). Graduated to `v1` in v1.6.0. | [docs](https://gateway-api.sigs.k8s.io/reference/api-types/tcproute/) |
| `UDPRoute` | `gateway.networking.k8s.io/v1alpha2` | L4 UDP routing. Graduated to `v1` in v1.6.0. | [docs](https://gateway-api.sigs.k8s.io/reference/api-types/udproute/) |
| `XMesh` | `gateway.networking.x-k8s.io/v1alpha1` | Mesh-wide configuration, supported features, and default service-to-service policy scope (GEP-3949 "Mesh Resource"). | [GEP-3949](https://gateway-api.sigs.k8s.io/geps/gep-3949/) |
| `XBackendTrafficPolicy` | `gateway.networking.x-k8s.io/v1alpha1` | Extended per-backend traffic policy (extended `BackendTLSPolicy` behavior). | [docs](https://gateway-api.sigs.k8s.io/reference/api-types/policy/backendtrafficpolicy/) |

Notes:

- Channel/version choice: the [CRD management guide](https://gateway-api.sigs.k8s.io/guides/crd-management/) documents installing/updating/rolling back between channels. Since v1.5.0 both channel installs ship a `ValidatingAdmissionPolicy` (`policy/v1`) named **`safe-upgrades.gateway.networking.k8s.io`** which blocks installing Experimental CRDs over Standard and downgrades below v1.5 — delete the VAP first if you must do either.
- The v1.5.x **experimental** CRDs exceed the 256KiB annotation limit of a normal client-side `kubectl apply` (use `kubectl apply --server-side=true`); GitOps tooling like Flux already uses server-side apply.
- `ExtensionRef` is not a kind: it is the extension mechanism by which routes reference implementation-specific resources (e.g. Traefik's `Middlewares` from group `traefik.io`) via `spec.rules[].filters` or `backendRefs`.
- XMesh has no dedicated current api-types page; see the [v1.5 spec](https://gateway-api.sigs.k8s.io/reference/api-spec/1.5/spec/) and GEP-3949.
