---
upstream: https://github.com/traefik/traefik
last_updated: 2026-08-17
---

# traefik — API reference

The Kubernetes API surface Traefik consumes, split by provider. K8s CRD kinds are grouped under **`traefik.io`** (all served at `v1alpha1`); Gateway API kinds live under **`gateway.networking.k8s.io`**; the Ingress provider consumes the core **`networking.k8s.io/v1 Ingress`**.

The canonical CRD manifests (apply these before upgrading the Traefik image when a release changes the CRD shape) are published in the upstream repo per version line, e.g. `v3.7`: [kubernetes-crd-definition-v1.yml](https://raw.githubusercontent.com/traefik/traefik/v3.7/docs/content/reference/dynamic-configuration/kubernetes-crd-definition-v1.yml). Field-level documentation is intentionally not duplicated here; the per-kind links are authoritative.

## Kubernetes CRD provider (`traefik.io/v1alpha1`)

Provider setup: [kubernetes-crd](https://doc.traefik.io/traefik/v3.7/reference/install-configuration/providers/kubernetes/kubernetes-crd/).

| Kind | API group/version | Purpose | Upstream API docs |
| ---- | ----------------- | ------- | ----------------- |
| `IngressRoute` | `traefik.io/v1alpha1` | HTTP router: entrypoints, rule (matchers), middleware refs, TLS options, service refs. | [docs](https://doc.traefik.io/traefik/v3.7/reference/routing-configuration/kubernetes/crd/http/ingressroute/) |
| `TraefikService` | `traefik.io/v1alpha1` | Declarative service definition: weighted load balancing, mirroring, failover, health check, load balancer settings; also an aggregation point for dynamic services. | [docs](https://doc.traefik.io/traefik/v3.7/reference/routing-configuration/kubernetes/crd/http/traefikservice/) |
| `Middleware` | `traefik.io/v1alpha1` | HTTP middleware instances (auth, rate limiting, headers, retries, error pages, ...). | [docs](https://doc.traefik.io/traefik/v3.7/reference/routing-configuration/kubernetes/crd/http/middleware/) |
| `ServersTransport` | `traefik.io/v1alpha1` | HTTP server-side transport config: TLS verification/certificates for the *backend* side, proxy protocol (server), load-balancing tuning. | [docs](https://doc.traefik.io/traefik/v3.7/reference/routing-configuration/kubernetes/crd/http/serverstransport/) |
| `IngressRouteTCP` | `traefik.io/v1alpha1` | TCP router: entrypoint, service refs, TLS (incl. passthrough/SAN termination), `MiddlewareTCP` refs. | [docs](https://doc.traefik.io/traefik/v3.7/reference/routing-configuration/kubernetes/crd/tcp/ingressroutetcp/) |
| `MiddlewareTCP` | `traefik.io/v1alpha1` | TCP middleware (IP allowlist, transparent client port). | [docs](https://doc.traefik.io/traefik/v3.7/reference/routing-configuration/kubernetes/crd/tcp/middlewaretcp/) |
| `ServersTransportTCP` | `traefik.io/v1alpha1` | TCP server-side transport config: proxy protocol, TLS verification for the backend side. | [docs](https://doc.traefik.io/traefik/v3.7/reference/routing-configuration/kubernetes/crd/tcp/serverstransporttcp/) |
| `TLSStore` | `traefik.io/v1alpha1` | Collection of server certificates (SNI) presented by Traefik; references Kubernetes `Secret`s. | [docs](https://doc.traefik.io/traefik/v3.7/reference/routing-configuration/kubernetes/crd/tls/tlsstore/) |
| `TLSOption` | `traefik.io/v1alpha1` | Per-`IngressRoute`/listener TLS options: cipher suites, min version, SNIs, certificates to present. | [docs](https://doc.traefik.io/traefik/v3.7/reference/routing-configuration/kubernetes/crd/tls/tlsoption/) |
| `IngressRouteUDP` | `traefik.io/v1alpha1` | UDP router: entrypoint (UDP) and the destination service. | [docs](https://doc.traefik.io/traefik/v3.7/reference/routing-configuration/kubernetes/crd/udp/ingressrouteudp/) |

Notes:

- `Service` in these docs is the core Kubernetes `v1` Service being *referenced* (e.g. by `IngressRoute.spec.service` or `TraefikService.spec.loadBalancer`); there is no `traefik.io` `Service` kind. Reference: [crd/http/service](https://doc.traefik.io/traefik/v3.7/reference/routing-configuration/kubernetes/crd/http/service/).
- Kinds are referenceable across namespaces per the `crossProviderNamespaces` rules — v3.7 added `crossProviderNamespaces` to explicitly allow cross-provider references (CRD ↔ Ingress/Gateway), see [v3.7 migration guide](https://doc.traefik.io/traefik/v3.7/migrate/v3/#v371).

## Gateway API provider (`gateway.networking.k8s.io`)

Provider setup: [kubernetes-gateway](https://doc.traefik.io/traefik/v3.7/reference/install-configuration/providers/kubernetes/kubernetes-gateway/). Route/resource reference: [kubernetes/gateway-api](https://doc.traefik.io/traefik/v3.7/reference/routing-configuration/kubernetes/gateway-api/).

| Kind | API version | Purpose |
| ---- | ----------- | ------- |
| `GatewayClass` | `v1` | Declares that Traefik handles a class of `Gateway`s (bound via `gatewayClassName`). |
| `Gateway` | `v1` | A listener set (entrypoint, address/hostname, protocol) owned by a traffic consumer. |
| `HTTPRoute` | `v1` | HTTP routing rules with filters, backends, and headers. |
| `GRPCRoute` | `v1` | gRPC (HTTP/2) routing rules. |
| `TCPRoute` | `v1alpha2` | Raw TCP routes. Requires the experimental-channel Gateway API CRDs (see note below). |
| `TLSRoute` | `v1alpha2` | TLS passthrough routes. Requires the experimental-channel Gateway API CRDs. |

Notes:

- Traefik implements `Gateway`/`GatewayClass`/`HTTPRoute`/`GRPCRoute` as *Standard* channel, and `TCPRoute`/`TLSRoute` as *Experimental*.
- As of v3.7.x, Traefik supports Gateway API spec v1.6.1; the `TCPRoute`/`TLSRoute` kinds are not served by the standard-channel v1.6.1 CRDs, so the experimental-channel CRDs must be installed in the cluster or the provider will not finish starting (no logs produced) — [v3.7 migration guide, v3.7.10](https://doc.traefik.io/traefik/v3.7/migrate/v3/#v3710).
- Traefik middlewares are reachable from Gateway API resources via the `ExtensionRef` `kind: Middleware` API group `traefik.io`.

## Ingress provider (`networking.k8s.io/v1`)

- Ingress provider setup: [kubernetes-ingress](https://doc.traefik.io/traefik/v3.7/reference/install-configuration/providers/kubernetes/kubernetes-ingress/). Consumes the core `Ingress` kind with `kubernetes.io/ingress.class` annotation and `networking.k8s.io/v1 IngressClass`; annotations under the `traefik.io/` prefix map to rule, middleware, and TLS options.
- Reference table for the Ingress surface: [reference/routing-configuration/kubernetes/ingress](https://doc.traefik.io/traefik/v3.7/reference/routing-configuration/kubernetes/ingress/).

## Ingress NGINX provider (`networking.k8s.io/v1` + `nginx.ingress.kubernetes.io/*`)

Provider setup: [kubernetes-ingress-nginx](https://doc.traefik.io/traefik/v3.7/reference/install-configuration/providers/kubernetes/kubernetes-ingress-nginx/). Consumes `Ingress` resources annotated with `kubernetes.io/ingress.class: nginx` and translates the `nginx.ingress.kubernetes.io/*` annotation set into Traefik routing. GA in **v3.7** — requires extra RBAC (list/watch `configmaps`) on the Traefik ServiceAccount, see [v3.7 migration guide, v3.7.0](https://doc.traefik.io/traefik/v3.7/migrate/v3/#v370).

Reference table: [reference/routing-configuration/kubernetes/ingress-nginx](https://doc.traefik.io/traefik/v3.7/reference/routing-configuration/kubernetes/ingress-nginx/).

## Knative provider

Provider setup: [knative](https://doc.traefik.io/traefik/v3.7/reference/install-configuration/providers/kubernetes/knative/). Consumes `kserve.knative.dev/v1 ServingKService` (and related Knative serving objects) to surface Knative Services.

Reference table: [reference/routing-configuration/kubernetes/knative](https://doc.traefik.io/traefik/v3.7/reference/routing-configuration/kubernetes/knative/).
