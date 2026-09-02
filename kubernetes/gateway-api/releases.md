---
upstream: https://github.com/kubernetes-sigs/gateway-api
last_updated: 2026-08-23
---

# gateway-api — Latest 10 official releases (newest first)

GA releases only (pre-releases like `-rc.x`/alpha tags excluded). Release notes: [GitHub releases page](https://github.com/kubernetes-sigs/gateway-api/releases).

## [v1.6.1](https://github.com/kubernetes-sigs/gateway-api/releases/tag/v1.6.1) — 2026-07-16

Patch release. Conformance/test suite fixes only: TCPRoute/UDPRoute multi-route attachment test port, `CleanupTestResources` handling, `SetupTimeoutConfig()` default handling, timeouts previously ignored, and flake fixes in `GatewayStaticAddresses`, `GatewayInfrastructure`, and TCP/UDP weighted routing tests. No API changes.

## [v1.6.0](https://github.com/kubernetes-sigs/gateway-api/releases/tag/v1.6.0) — 2026-06-29

- **`UDPRoute` and `TCPRoute` graduate to GA** (`v1` API version recommended; the `v1alpha2` versions are **deprecated** and scheduled for removal — #4923, #4783, #5030). This is the main API event for clusters on experimental CRDs upgrading from v1.5.x.
- Validation tightening: `HTTPRoute` `retry.codes` must be unique and `retry.attempts >= 1` (#4907); `certificateRefs`/CA references limit raised 8 → 16 (#4088); `TLSRoute` allowed up to 1024 hostnames/rules per resource (#4332 — verify kube-apiserver, etcd, and controller behavior first).
- `BackendTLSPolicy` can now be used alongside other route types (#4745); up to 16 annotations allowed on gateway infrastructure objects (#4707); XBackend GEP added to Experimental (#4488); `idleTimeout` removed from the Experimental `SessionPersistence` API (#4771).
- New `GATEWAY-UDP` conformance profile plus UDPRoute (GEP-2645) conformance tests and `SupportTCPRoute` feature.
- **Documentation site migrated from MkDocs to Docsy** — the old `/reference/spec/` URL is gone (now `/reference/api-types/` per kind and `/reference/api-spec/<version>/spec/` per release); new implementation [wizard](https://gateway-api.sigs.k8s.io/wizard).
- Conformance: GRPC/HTTPRoute hostname co-location is now optional for implementations.

## [v1.5.1](https://github.com/kubernetes-sigs/gateway-api/releases/tag/v1.5.1) — 2026-03-14

Patch release: conformance fixes (IPv6 support, `conflicted=false` no longer required for non-conflicted listeners, TLSRoute FIN/RST test relaxation, CORS test tweaks, misdirected-requests test limited to h2) and a CEL fix disallowing repeated CORS filters. Carries the standing warning that **Experimental channel CRDs are too large for a standard `kubectl apply`** (use `kubectl apply --server-side=true`).

## [v1.5.0](https://github.com/kubernetes-sigs/gateway-api/releases/tag/v1.5.0) — 2026-02-27

⚠️ Breaking/upgrade notes: `TLSRoute v1alpha2` moves to Experimental-only (removed from Experimental entirely in 1.6); `TLSRoute` CEL validation requires **Kubernetes ≥ 1.31**; the new `safe-upgrades.gateway.networking.k8s.io` ValidatingAdmissionPolicy blocks installing Experimental CRDs over Standard and blocks downgrades below 1.5 — delete the VAP first if you must.

- **Standard channel GA:** Gateway client certificate validation (GEP-91, GEP-3567), certificate selection for Gateway TLS origination (GEP-3155), `ListenerSet` (GEP-1713), `HTTPRoute` CORS filter (GEP-1767), `TLSRoute` v1 (GEP-2643).
- `ReferenceGrant` graduates to `v1`.
- Experimental: Gateway/`HTTPRoute`-level external authentication (GEP-1494).

## [v1.4.1](https://github.com/kubernetes-sigs/gateway-api/releases/tag/v1.4.1) — 2025-12-04

Patch release. ⚠️ The install YAML originally published on 2025-12-04 mistakenly included experimental changes (PRs #3774, #3823, #4158) in the Standard channel YAMLs; the maintainers corrected the published files in-place on 2026-02-10, so artifacts fetched before that date differ from current ones. `BackendTLSPolicy` temporarily restricted to a single `targetRef` per policy (#4316/#4298, expected to be lifted later); SAN validation correctly marked Standard; `BackendTLSPolicy` `status` correctly a subresource; BackendTLSPolicy conformance tests folded into the GATEWAY-HTTP profile.

## [v1.4.0](https://github.com/kubernetes-sigs/gateway-api/releases/tag/v1.4.0) — 2025-10-06

- ⚠️ **Experimental CORS `allowCredentials` type change:** enum-of-boolean → plain boolean; tooling/templates evaluating experimental CORS support must adapt (#3895).
- ⚠️ `GRPCRoute.spec` became explicitly required (previously unintentionally omitted via `omitempty`) — a technical backward-incompatible change.
- **Standard channel GA:** `BackendTLSPolicy` (GEP-1897 — TLS from gateway to backends) and SupportedFeatures (GEP-3164 — implementation feature reporting in `GatewayClass` status).
- New Experimental features: Mesh Resource / `XMesh` (GEP-3949), Default Gateways (GEP-3793 — `Gateway`s can program some routes by default), HTTP Route external auth (GEP-1494).
- Standing warning from this release: Experimental CRDs exceed the 262144-byte annotation limit of client-side `kubectl apply`.

## [v1.3.0](https://github.com/kubernetes-sigs/gateway-api/releases/tag/v1.3.0) — 2025-04-24

- `OverlappingTLSConfig` Listener condition added to surface connection-coalescing (SNI/hostname overlap) problems; spec now recommends HTTP 421 responses in certain cases (GEP-3567).
- Percentage-based request mirroring graduates to Standard (GEP-3171).
- `GRPCRoute` match limit raised 8 → 64; new conformance test ensuring `backendRef` filters don't break weighted routing.
- `Gateway.spec.addresses` type changed from `[]GatewayAddress` to `[]GatewaySpecAddress` with `value` now optional (implementation may auto-assign, else set `Programmed=False/AddressNotAssigned`).
- `BackendTLSPolicy` `SubjectAltNames` moved Core → Extended.

## [v1.2.1](https://github.com/kubernetes-sigs/gateway-api/releases/tag/v1.2.1) — 2024-11-29

Patch. Fixes the backward incompatibility in the `SupportedFeatures` field of the `GatewayClass` API introduced by v1.2.0 (#3454).

## [v1.1.1](https://github.com/kubernetes-sigs/gateway-api/releases/tag/v1.1.1) — 2024-10-31

Patch. Fixes GRPCRoute `v1alpha2` status (restored as subresource with previous printer columns, #3412), relaxes CEL validation for header-based session persistence to allow a missing `AbsoluteTimeout` (#3215), plus CI and conformance suite improvements.

## [v1.2.0](https://github.com/kubernetes-sigs/gateway-api/releases/tag/v1.2.0) — 2024-10-03

⚠️ **Removal:** the `v1alpha2` versions of `GRPCRoute` and `ReferenceGrant` are no longer served by either channel's CRDs — confirm implementations (not just your YAML) consume `v1` before upgrading; `v1alpha2` can also get stuck in CRD `status.storedVersions` and may need manual migration (the release notes include `kubectl`/jq migration steps).

- **Standard channel GA:** gateway infrastructure labels/annotations (GEP-1867/GEP-1762), `HTTPRoute` timeouts (GEP-1742/GEP-2257), BackendProtocol.
- New Experimental features: CORS (GEP-1767), percentage-based mirroring (GEP-3171), certificate selection (GEP-3155), session persistence.
- First release under the newer semi-annual-style release cycle (two releases/quarter pattern).
