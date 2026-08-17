---
upstream: https://github.com/traefik/traefik
last_updated: 2026-08-17
---

# traefik — releases

Latest 10 official releases, newest first. Traefik maintains **three supported version lines** (e.g. `v3.7.x`, `v3.6.x`, `v2.11.x`); patches for CVEs and Kubernetes-provider fixes land on all of them, so watch the whole set, not just the newest line. Check ⚠️ entries before upgrading. Version-line migration guides: [v3.7](https://doc.traefik.io/traefik/v3.7/migrate/v3/), [v3.6](https://doc.traefik.io/traefik/v3.6/migrate/v3/), [v2.11](https://doc.traefik.io/traefik/v2.11/migration/v2/).

## v3.7.10 — 2026-07-31

[Release page](https://github.com/traefik/traefik/releases/tag/v3.7.10)

- Security fixes: [GHSA-fgjj-px3w-67xx](https://github.com/advisories/GHSA-fgjj-px3w-67xx), [GHSA-62fc-8686-hfmq](https://github.com/advisories/GHSA-62fc-8686-hfmq), [GHSA-6765-c87h-8mrf](https://github.com/advisories/GHSA-6765-c87h-8mrf).
- ⚠️ **Kubernetes Gateway API provider: generated router, middleware, and service names change** — the hash suffix is now derived from all route/Gateway/listener identifying fields (not just the rule), and generated *service* names are prefixed with the route rule. Names appear in dashboards, access logs (`RouterName`/`ServiceName`), and metrics labels: matching queries must be updated. [v3.7 migration guide](https://doc.traefik.io/traefik/v3.7/migrate/v3/#v3710)
- ⚠️ **Gateway API spec v1.6.1 support**: `TCPRoute`/`TLSRoute` are not served by the standard-channel v1.6.1 CRDs — install the experimental-channel CRDs or the Gateway API provider will not start. [v3.7 migration guide](https://doc.traefik.io/traefik/v3.7/migrate/v3/#v3710)
- Bug fixes: authentication singleflight key-collision fix ([#13572](https://github.com/traefik/traefik/pull/13572)), Gateway API router name collision ([#13580](https://github.com/traefik/traefik/pull/13580)), lego v5.3.1 ([#13547](https://github.com/traefik/traefik/pull/13547)), dd-trace-go 2.8.1.

## v3.6.25 — 2026-07-31

[Release page](https://github.com/traefik/traefik/releases/tag/v3.6.25)

- Security fixes: same three advisories as v3.7.10 ([GHSA-fgjj-px3w-67xx](https://github.com/advisories/GHSA-fgjj-px3w-67xx), [GHSA-62fc-8686-hfmq](https://github.com/advisories/GHSA-62fc-8686-hfmq), [GHSA-6765-c87h-8mrf](https://github.com/advisories/GHSA-6765-c87h-8mrf)).
- ⚠️ **Kubernetes Gateway API provider: same generated router/middleware/service name change as in v3.7.10** (hash now over all route/Gateway/listener fields; service names prefixed with the route rule). Update dashboards/log queries. [v3.6 migration guide](https://doc.traefik.io/traefik/v3.6/migrate/v3/#v3625)

## v2.11.54 — 2026-07-31

[Release page](https://github.com/traefik/traefik/releases/tag/v2.11.54)

- Security fix: [GHSA-62fc-8686-hfmq](https://github.com/advisories/GHSA-62fc-8686-hfmq).
- Dependency bumps: dd-trace-go, `golang.org/x/text`, `golang.org/x/net`.
- Bug fix: cross-namespace service reference check (Kubernetes CRD provider).

## v3.7.9 — 2026-07-24

[Release page](https://github.com/traefik/traefik/releases/tag/v3.7.9)

- Security fix: [GHSA-3ccp-42pg-hgv6](https://github.com/advisories/GHSA-3ccp-42pg-hgv6).
- ⚠️ **HTTP/1 `CONNECT` requests are now rejected with `501 Not Implemented`** (they were non-functional before, the rejection makes it explicit). [v3.7 migration guide](https://doc.traefik.io/traefik/v3.7/migrate/v3/#v379)
- Deprecates Zstd in `gzhttp` (HTTP/2 + Zstd); defers `CONNECT` payload handling; fixes `use-regex` redirect handling in the Ingress NGINX provider.

## v3.6.24 — 2026-07-24

[Release page](https://github.com/traefik/traefik/releases/tag/v3.6.24)

- Security fix: [GHSA-3ccp-42pg-hgv6](https://github.com/advisories/GHSA-3ccp-42pg-hgv6).
- ⚠️ Kubernetes CRDs updated: adds the previously-missing `Errors.middleware.errorRequestHeaders` option to the `Middleware` CRD — **apply the v3.6 CRD manifest in-cluster before upgrading** (the option is only usable once the CRDs are updated).
- Mirrors the v3.7.9 HTTP/1 `CONNECT` → `501` behavior on the v3.6 line.

## v2.11.53 — 2026-07-24

[Release page](https://github.com/traefik/traefik/releases/tag/v2.11.53)

- Security fix: [GHSA-3ccp-42pg-hgv6](https://github.com/advisories/GHSA-3ccp-42pg-hgv6).
- ⚠️ **Kubernetes CRDs must be updated before upgrading**: the `Errors` middleware `errorRequestHeaders` option (introduced in v2.11.44) is now exposed on the `Middleware` CRD. [v2.11 migration guide](https://doc.traefik.io/traefik/v2.11/migration/v2/#v21153)
- ⚠️ HTTP/1 `CONNECT` requests rejected with `501 Not Implemented`. [v2.11 migration guide](https://doc.traefik.io/traefik/v2.11/migration/v2/#v21153)

## v3.7.8 — 2026-07-15

[Release page](https://github.com/traefik/traefik/releases/tag/v3.7.8)

- Security fix: [GHSA-8rxv-jg7p-wvg3](https://github.com/advisories/GHSA-8rxv-jg7p-wvg3) (injection via rewritten targets in the Ingress NGINX provider, now sanitized).
- ⚠️ **Kubernetes CRDs must be updated before upgrading**: `errorRequestHeaders` added to the `Middleware` CRD. [v3.7 migration guide](https://doc.traefik.io/traefik/v3.7/migrate/v3/#v378)
- Fixes a panic in the `Retry` middleware when backends switch to WebSockets.

## v3.7.7 — 2026-07-08

[Release page](https://github.com/traefik/traefik/releases/tag/v3.7.7)

- Security fixes: [GHSA-cxjq-mrr5-89rv](https://github.com/advisories/GHSA-cxjq-mrr5-89rv), [GHSA-42cj-m3vj-89wv](https://github.com/advisories/GHSA-42cj-m3vj-89wv), [GHSA-qq9q-x9w4-chhj](https://github.com/advisories/GHSA-qq9q-x9w4-chhj).
- ⚠️ **`Host(*)` is now a catch-all** (previously only `HostSNI(*)` matched any domain; `Host(*)` matched single-label labels). Rules relying on the old single-label behavior must be rewritten. [v3.7 migration guide](https://doc.traefik.io/traefik/v3.7/migrate/v3/#v377)
- Fixes Gateway API `ExtensionRef` (Traefik `Middleware`) not being applied.

## v3.6.23 — 2026-07-08

[Release page](https://github.com/traefik/traefik/releases/tag/v3.6.23)

- Security fixes: [GHSA-cxjq-mrr5-89rv](https://github.com/advisories/GHSA-cxjq-mrr5-89rv), [GHSA-42cj-m3vj-89wv](https://github.com/advisories/GHSA-42cj-m3vj-89wv).
- Fixes a panic on nil `EndpointSlice` ports (in-cluster provider) and tightens the cross-provider reference check for TCP `ServersTransport`.

## v2.11.52 — 2026-07-08

[Release page](https://github.com/traefik/traefik/releases/tag/v2.11.52)

- Security fix: [GHSA-cxjq-mrr5-89rv](https://github.com/advisories/GHSA-cxjq-mrr5-89rv) — the `ReplacePathRegex` replacement path is now sanitized.
- Dependency bumps: `golang.org/x/crypto/pkcs12`, OpenTelemetry SDK.

> **Watch-list (documented, not yet released as of 2026-08-17):** the [v3.7 migration guide](https://doc.traefik.io/traefik/v3.7/migrate/v3/#v3711) already documents **v3.7.11** (a `core.strictTLSOptions` fail-closed flag for conflicting TLS options, and hash-based generated names for Kubernetes CRD resources) and the [v2.11 migration guide](https://doc.traefik.io/traefik/v2.11/migration/v2/#v21155) documents **v2.11.55** (Gateway API generated router/service name quoting change). Verify release status before relying on either.
