---
upstream: https://github.com/traefik/traefik
last_updated: 2026-08-17
---

# traefik — features

Key feature areas, each linked to the upstream documentation (v3.7 line) covering it. Traefik's configuration is split into *static* (entrypoints, providers, TLS resolvers, observability — set at startup) and *dynamic* (routers, services, middlewares — hot-reloaded from the watched Kubernetes resources); both are documented under [reference](https://doc.traefik.io/traefik/v3.7/reference/).

## Kubernetes integration

- **Kubernetes CRD provider**: Traefik's own `traefik.io` CRDs (`IngressRoute`, `TraefikService`, `Middleware`, `ServersTransport`, TCP/TLS/UDP variants) give full dynamic-config control — weighted load balancing, mirroring, failover, per-route TLS — beyond what `Ingress` annotations can express. [docs](https://doc.traefik.io/traefik/v3.7/reference/install-configuration/providers/kubernetes/kubernetes-crd/)
- **Ingress provider**: consumes core `Ingress` objects (class + `traefik.io/` annotations). [docs](https://doc.traefik.io/traefik/v3.7/reference/install-configuration/providers/kubernetes/kubernetes-ingress/)
- **Gateway API provider**: implements `GatewayClass`, `Gateway`, `HTTPRoute`, `GRPCRoute` (Standard) and `TCPRoute`/`TLSRoute` (Experimental channel), with Traefik `Middleware` reachable via `ExtensionRef`. [docs](https://doc.traefik.io/traefik/v3.7/reference/install-configuration/providers/kubernetes/kubernetes-gateway/)
- **Ingress NGINX provider**: translates `nginx.ingress.kubernetes.io/*` annotations for existing Nginx-Ingress fleets (GA in v3.7; requires `configmaps` list/watch RBAC). [docs](https://doc.traefik.io/traefik/v3.7/reference/install-configuration/providers/kubernetes/kubernetes-ingress-nginx/)
- **Knative provider**: exposes Knative services. [docs](https://doc.traefik.io/traefik/v3.7/reference/install-configuration/providers/kubernetes/knative/)
- **Multi-tenant Kubernetes**: network isolation between Traefik namespaces and user namespaces, plus cross-namespace/cross-provider resource reference rules (`crossProviderNamespaces` since v3.7.1). [docs](https://doc.traefik.io/traefik/v3.7/security/multi-tenant-kubernetes/)

## Routing

- **HTTP routing rules (v3 syntax)**: matchers (`Host`, `PathPrefix`, `Path`, `HeadersRegexp`, `Query`, ...), priority, wildcards, and rule precedence. `Host(*)` became a catch-all in v3.7.7. [HTTP rules & priority](https://doc.traefik.io/traefik/v3.7/reference/routing-configuration/http/routing/rules-and-priority/), [TCP rules & priority](https://doc.traefik.io/traefik/v3.7/reference/routing-configuration/tcp/routing/rules-and-priority/), [UDP rules & priority](https://doc.traefik.io/traefik/v3.7/reference/routing-configuration/udp/routing/rules-priority/)
- **Entry endpoints and redirects**: entrypoint configuration, HTTP→HTTPS redirect schemes. [entrypoints](https://doc.traefik.io/traefik/v3.7/reference/install-configuration/entrypoints/), [expose on Kubernetes](https://doc.traefik.io/traefik/v3.7/expose/kubernetes/basic/) (+ [advanced](https://doc.traefik.io/traefik/v3.7/expose/kubernetes/advanced/))

## Middlewares

Composable HTTP middleware pipeline (auth, rate limiting, headers, retries, error pages, redirects, compress, ...): [middleware overview](https://doc.traefik.io/traefik/v3.7/reference/routing-configuration/http/middlewares/overview/). Selected middlewares: [basic auth](https://doc.traefik.io/traefik/v3.7/reference/routing-configuration/http/middlewares/basicauth/), [forward auth](https://doc.traefik.io/traefik/v3.7/reference/routing-configuration/http/middlewares/forwardauth/), [rate limit](https://doc.traefik.io/traefik/v3.7/reference/routing-configuration/http/middlewares/ratelimit/), [error pages](https://doc.traefik.io/traefik/v3.7/reference/routing-configuration/http/middlewares/errorpages/), [headers](https://doc.traefik.io/traefik/v3.7/reference/routing-configuration/http/middlewares/headers/), [redirect scheme](https://doc.traefik.io/traefik/v3.7/reference/routing-configuration/http/middlewares/redirectscheme/), [gRPC-web](https://doc.traefik.io/traefik/v3.7/reference/routing-configuration/http/middlewares/grpcweb/), [circuit breaker](https://doc.traefik.io/traefik/v3.7/reference/routing-configuration/http/middlewares/circuitbreaker/). TCP middlewares (IP allowlist, transparent port) are part of the [IngressRouteTCP](https://doc.traefik.io/traefik/v3.7/reference/routing-configuration/kubernetes/crd/tcp/ingressroutetcp/) / [MiddlewareTCP](https://doc.traefik.io/traefik/v3.7/reference/routing-configuration/kubernetes/crd/tcp/middlewaretcp/) surface.

## Load balancing and services

- **Services**: `Service` (Kubernetes backend reference) and declarative `TraefikService` (weighted LB, mirroring, failover, health check). [docs](https://doc.traefik.io/traefik/v3.7/reference/routing-configuration/http/load-balancing/service/)
- **ServersTransport**: backend-side tuning — retries, response forwarding timeout, TLS verification/certificates to backends, proxy protocol (server). [docs](https://doc.traefik.io/traefik/v3.7/reference/routing-configuration/http/load-balancing/serverstransport/)

## TLS

- **Server-side TLS (Kubernetes)**: `TLSStore` (SNI certificates from `Secret`s) and `TLSOption` (ciphers, min version, per-route certificate selection) per route/entrypoint. [TLSStore](https://doc.traefik.io/traefik/v3.7/reference/routing-configuration/kubernetes/crd/tls/tlsstore/), [TLSOption](https://doc.traefik.io/traefik/v3.7/reference/routing-configuration/kubernetes/crd/tls/tlsoption/)
- **Certificate resolvers**: automatic certificate acquisition and renewal — [overview](https://doc.traefik.io/traefik/v3.7/reference/install-configuration/tls/certificate-resolvers/overview/), [ACME (Let's Encrypt, etc.)](https://doc.traefik.io/traefik/v3.7/reference/install-configuration/tls/certificate-resolvers/acme/), [Tailscale](https://doc.traefik.io/traefik/v3.7/reference/install-configuration/tls/certificate-resolvers/tailscale/)
- **OCSP stapling**: [docs](https://doc.traefik.io/traefik/v3.7/reference/install-configuration/tls/ocsp/)
- **SPIFFE workload identity**: [docs](https://doc.traefik.io/traefik/v3.7/reference/install-configuration/tls/spiffe/)

## Observability

- **Metrics** (Prometheus, Datadog, etc.), **access logs** (JSON, fields incl. `RouterName`/`ServiceName`), **healthcheck** endpoint, and **tracing** (OpenTelemetry, etc.). [metrics](https://doc.traefik.io/traefik/v3.7/reference/install-configuration/observability/metrics/), [logs & access logs](https://doc.traefik.io/traefik/v3.7/reference/install-configuration/observability/logs-and-accesslogs/), [healthcheck](https://doc.traefik.io/traefik/v3.7/reference/install-configuration/observability/healthcheck/), [tracing](https://doc.traefik.io/traefik/v3.7/reference/install-configuration/observability/tracing/)

## Plugins

Community plugins extend the middleware set with code compiled into the binary at build time (experimental feature). [docs](https://doc.traefik.io/traefik/v3.7/reference/install-configuration/experimental/plugins/)
