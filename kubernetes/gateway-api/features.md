---
upstream: https://github.com/kubernetes-sigs/gateway-api
last_updated: 2026-08-23
---

# gateway-api — Features

Feature areas of the Gateway API specification with the upstream documentation for each. Graduation versions are given from the [release notes](releases.md) (Standard channel = generally available; Experimental = pre-graduation).

## Core traffic model

The pipeline `GatewayClass` (which controller class) → `Gateway` (an instance of infrastructure with Listeners) → route resources (`HTTPRoute`, `GRPCRoute`, `TLSRoute`, …) is the heart of the API, with `ReferenceGrant` as the cross-namespace authorization gate.

- API overview / concepts: https://gateway-api.sigs.k8s.io/docs/concepts/api-overview/
- Traffic matching model: https://gateway-api.sigs.k8s.io/docs/concepts/traffic-matching/
- Simple gateway walkthrough: https://gateway-api.sigs.k8s.io/guides/getting-started/simple-gateway/
- Migrating from Ingress: https://gateway-api.sigs.k8s.io/guides/getting-started/migrating-from-ingress/

## HTTP routing (L7, `HTTPRoute`)

- HTTP routing guide (paths, headers, query params, methods, hostnames): https://gateway-api.sigs.k8s.io/guides/user-guides/http-routing/
- Timeouts (request/response/idle; GA in v1.2.0): https://gateway-api.sigs.k8s.io/guides/user-guides/http-timeouts/
- Retries and retry budget ([GEP-1247](https://gateway-api.sigs.k8s.io/geps/gep-1247/)): https://gateway-api.sigs.k8s.io/geps/gep-1247/
- Traffic splitting / weighted backends: https://gateway-api.sigs.k8s.io/guides/user-guides/traffic-splitting/
- Request mirroring, including percentage-based mirroring (GA in v1.3.0, [GEP-3171](https://gateway-api.sigs.k8s.io/geps/gep-3171/)): https://gateway-api.sigs.k8s.io/guides/user-guides/http-request-mirroring/
- CORS filter (Experimental in v1.2.0, GA in v1.5.0, [GEP-1767](https://gateway-api.sigs.k8s.io/geps/gep-1767/)): https://gateway-api.sigs.k8s.io/guides/user-guides/http-cors/
- Header modification: https://gateway-api.sigs.k8s.io/guides/user-guides/http-header-modifier/
- Redirects and rewrites: https://gateway-api.sigs.k8s.io/guides/user-guides/http-redirect-rewrite/

## gRPC routing (L7, `GRPCRoute`)

- gRPC routing guide (service/method matching, mirrors, timeouts): https://gateway-api.sigs.k8s.io/guides/user-guides/grpc-routing/
- API type: https://gateway-api.sigs.k8s.io/reference/api-types/grpcroute/ — `v1` since v1.1; `v1alpha2` removed in v1.2.0.

## L4 routing (TCP/TLS/UDP)

- TCP routing (`TCPRoute`; `v1alpha2` in v1.5.x, GA `v1` in v1.6.0): https://gateway-api.sigs.k8s.io/guides/user-guides/tcp/
- UDP routing (`UDPRoute`; GA `v1` in v1.6.0): https://gateway-api.sigs.k8s.io/guides/user-guides/udp/
- TLS passthrough routing (`TLSRoute`; GA `v1` in v1.5.0, [GEP-2643](https://gateway-api.sigs.k8s.io/geps/gep-2643/)): https://gateway-api.sigs.k8s.io/guides/user-guides/tls-routing/

## TLS and client certificates

- Listener TLS (termination, passthrough, certificates): https://gateway-api.sigs.k8s.io/guides/user-guides/tls/
- Backend-to-gateway TLS (`BackendTLSPolicy`, GA in v1.4.0, [GEP-1897](https://gateway-api.sigs.k8s.io/geps/gep-1897/)): https://gateway-api.sigs.k8s.io/reference/api-types/policy/backendtlspolicy/
- Gateway client certificate validation / mTLS (GA in v1.5.0; [GEP-91](https://gateway-api.sigs.k8s.io/geps/gep-91/), [GEP-3567](https://gateway-api.sigs.k8s.io/geps/gep-3567/)): https://gateway-api.sigs.k8s.io/geps/gep-3567/
- Certificate selection for TLS origination (GA in v1.5.0, [GEP-3155](https://gateway-api.sigs.k8s.io/geps/gep-3155/)): https://gateway-api.sigs.k8s.io/geps/gep-3155/
- Security concepts (ReferenceGrant threat model): https://gateway-api.sigs.k8s.io/docs/concepts/security/

## Cross-namespace routing (`ReferenceGrant`)

Multi-namespace setups (routes and/or listeners referencing backend Services or certificate Secrets in other namespaces) require explicit `ReferenceGrant` opt-in: https://gateway-api.sigs.k8s.io/guides/user-guides/multiple-ns/ — the API reference is at https://gateway-api.sigs.k8s.io/reference/api-types/referencegrant/ (`v1` since v1.5.0). This is the authorization model to know before wiring routes in this workspace's multi-namespace infrastructure.

## Listeners, ListenerSet, default Gateways

`ListenerSet` (GA in v1.5.0, [GEP-1713](https://gateway-api.sigs.k8s.io/geps/gep-1713/)) decouples listeners from the `Gateway` object via `Gateway.spec.allowedListeners`; **Default Gateways** ([GEP-3793](https://gateway-api.sigs.k8s.io/geps/gep-3793/)) let a `Gateway` program routes by default, reducing boilerplate for multi-tenant ingress:

- ListenerSet guide: https://gateway-api.sigs.k8s.io/guides/user-guides/listener-set/
- `ListenerSet` API type: https://gateway-api.sigs.k8s.io/reference/api-types/listenerset/

## Service mesh

The mesh profile ([GEP-1016](https://gateway-api.sigs.k8s.io/geps/gep-1016/)) covers service-to-service routing (mesh-local `GatewayClass`es), the `XMesh` resource for mesh-wide config/features (Experimental, [GEP-3949](https://gateway-api.sigs.k8s.io/geps/gep-3949/), [service facets](https://gateway-api.sigs.k8s.io/docs/mesh/service-facets/)): https://gateway-api.sigs.k8s.io/docs/mesh/mesh-overview/

## Conformance

Implementations declare support levels (Core/Extended/Implementation-specific, and per-feature `supportedFeatures` on `GatewayClass` since v1.4.0) and are tested by the conformance suite with profiles (GATEWAY-HTTP, GATEWAY-WEBSOCKETS, GATEWAY-GRPC, and GATEWAY-UDP from v1.6.0; TCP support added via the `SupportTCPRoute` feature): https://gateway-api.sigs.k8s.io/docs/concepts/conformance/ — useful when asking "does our Traefik GatewayClass actually do feature X?" (check `status.supportedFeatures` and linked conformance tests).

## Implementation-facing features

- Gateway infrastructure control (labels/annotations/`parametersRef` for underlying LBs, GA in v1.2.0): https://gateway-api.sigs.k8s.io/guides/user-guides/infrastructure/
- Implementers guide (conditions, status, how to build an implementation): https://gateway-api.sigs.k8s.io/guides/implementers-guide/
- API design rules for contributors: https://gateway-api.sigs.k8s.io/guides/api-design/
- Extension model: `ExtensionRef` references implementation-specific resources (e.g. `traefik.io` Middlewares/IngressRoute-style middlewares) from `HTTPRoute` filters and backend refs.

## Process and release management

- GEP (Gateway API Enhancement Proposals) process and index: https://gateway-api.sigs.k8s.io/geps/overview/ and https://gateway-api.sigs.k8s.io/geps/list/
- Versioning and channel (Standard/Experimental) model: https://gateway-api.sigs.k8s.io/docs/concepts/versioning/
- CRD install/update/rollback guide (channel upgrades, safe-upgrades VAP, server-side apply requirement): https://gateway-api.sigs.k8s.io/guides/crd-management/
- Per-release full API spec: e.g. [v1.5](https://gateway-api.sigs.k8s.io/reference/api-spec/1.5/spec/) (matches the cluster's installed bundle) and [v1.6](https://gateway-api.sigs.k8s.io/reference/api-spec/1.6/spec/).
