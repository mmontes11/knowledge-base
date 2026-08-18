---
upstream: https://github.com/jetstack/cert-manager
last_updated: 2026-08-16
---

# cert-manager — API reference

cert-manager serves 6 custom resource kinds across two API groups, both at version **`v1`**. The full generated reference is the [API docs page](https://cert-manager.io/docs/reference/api-docs/); the Go type definitions live in [`pkg/apis/`](https://github.com/jetstack/cert-manager/tree/master/pkg/apis) (`certmanager/v1` and `acme/v1`).

| Kind | Short name | Purpose | Upstream API docs |
| ---- | ---------- | ------- | ----------------- |
| `Certificate` | `cert`, `certs` | Core kind: requests a certificate from an `Issuer`/`ClusterIssuer` and keeps it renewed in a Secret — renewal windows, private key options, keystores, additional output formats. | [docs](https://cert-manager.io/docs/reference/api-docs/#cert-manager.io/v1.Certificate) |
| `CertificateRequest` | `cr`, `crs` | A direct signing request against an `Issuer`/`ClusterIssuer` for one certificate (no auto-renewal); the `Certificate` kind creates these under the hood. | [docs](https://cert-manager.io/docs/reference/api-docs/#cert-manager.io/v1.CertificateRequest) |
| `Issuer` | `iss` | Namespace-scoped definition of how certificates are obtained (ACME, CA, selfSigned, Vault, Venafi). | [docs](https://cert-manager.io/docs/reference/api-docs/#cert-manager.io/v1.Issuer) |
| `ClusterIssuer` | `ciss` | Cluster-scoped version of `Issuer`, referenceable from any namespace. | [docs](https://cert-manager.io/docs/reference/api-docs/#cert-manager.io/v1.ClusterIssuer) |
| `Challenge` | — | ACME challenge lifecycle (HTTP-01 or DNS-01); created and driven by the controller as part of order processing. | [docs](https://cert-manager.io/docs/reference/api-docs/#acme.cert-manager.io/v1.Challenge) |
| `Order` | — | Tracks an in-progress ACME order (authorizations + challenges) on the way to a signed certificate. | [docs](https://cert-manager.io/docs/reference/api-docs/#acme.cert-manager.io/v1.Order) |

Notes:

- All 6 kinds use API version `v1`, which is the served and storage version in the shipped `cert-manager.crds.yaml` release asset (verified against `v1.21.1`); every CRD enables the `status` subresource.
- `Challenge` and `Order` deliberately have no short names and are internal to cert-manager's ACME workflow (`Certificate` → `CertificateRequest` → `Order` → `Challenge`); since v1.20.3 / v1.19.6 / v1.21.0 the `cert-manager-edit` aggregated ClusterRole no longer permits users to create or modify them directly ([GHSA-8rvj-mm4h-c258](https://github.com/cert-manager/cert-manager/security/advisories/GHSA-8rvj-mm4h-c258)).
- Per-component configuration kinds are also served (`v1alpha1`): `ControllerConfiguration`, `WebhookConfiguration`, and `CAInjectorConfiguration` under the `*.config.cert-manager.io` groups, documented on the same [API docs page](https://cert-manager.io/docs/reference/api-docs/).
- The `pkg/apis/experimental/v1alpha1` package contains only annotation/key constants — it is not a served API kind.
- Field-level documentation is intentionally not duplicated here; follow the per-kind upstream links above.
