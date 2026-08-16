---
upstream: https://github.com/cert-manager/trust-manager
last_updated: 2026-08-16
---

# trust-manager — features

Key feature areas, each linked to the upstream documentation covering it. The [Trust Manager section of cert-manager.io](https://cert-manager.io/docs/trust/trust-manager/) is authoritative.

## Trust bundle sources

- **Certificate sources**: CA certificates are combined from ConfigMaps (including `binaryData` since v0.24.0), Secrets, inline PEM, or the system default CAs into a single trust bundle. [docs](https://cert-manager.io/docs/trust/trust-manager/), [v0.24.0](https://github.com/cert-manager/trust-manager/releases/tag/v0.24.0)
- **Default CA trust packages**: the default CA bundle is built from a Debian distro and distributed as images per release; the Trixie-based `trust-pkg-debian-trixie` package became the chart default in v0.23.0. On `ClusterBundle` it is controlled by `spec.defaultCAs.provider` (`System`/`Disabled`) and can be overridden with a custom package location. [v0.23.0](https://github.com/cert-manager/trust-manager/releases/tag/v0.23.0)
- **Non-CA certificate filtering** (v0.21.0): Helm value `.filterNonCACerts.enabled` drops non-CA certificates from sources based on X.509 `basicConstraints`. [v0.21.0](https://github.com/cert-manager/trust-manager/releases/tag/v0.21.0)

## Targets

- **ConfigMap/Secret targets**: the bundle is written into per-namespace `ConfigMap`s and/or `Secret`s selected by `namespaceSelector` (v1alpha2: required; empty `{}` matches all namespaces), with multiple keys and per-key formats (PEM, PKCS#12 with cipher profile) supported in v1alpha2. JKS was removed in v1alpha2. [API docs](https://cert-manager.io/docs/trust/trust-manager/api-reference/), [docs/alphav2-changes.md](https://github.com/cert-manager/trust-manager/blob/main/docs/alphav2-changes.md)
- **Target namespace scoping** (v0.20.0): restrict the operator to an explicit list of target namespaces. [v0.20.0](https://github.com/cert-manager/trust-manager/releases/tag/v0.20.0)

## API

- **`ClusterBundle` (v1alpha2)**: successor API to `Bundle` (`trust.cert-manager.io/v1alpha1`) with restructured inputs (`defaultCAs`/`inLineCAs`/`sourceRefs`) and richer targets; introduced to leave room for a future namespace-scoped `Bundle`, with an automatic migration path in progress. [docs/alphav2-changes.md](https://github.com/cert-manager/trust-manager/blob/main/docs/alphav2-changes.md)

## Deployment and operations

- **Helm**: installed via the Helm chart. Values include image registry/namespace for self-hosted registry mirroring (v0.22.0+), pod/container security contexts (v0.24.0+; superseding `app.securityContext.seccompProfileEnabled`), webhook TLS certificate duration override (v0.23.0+), and ServiceMonitor relabeling (v0.21.0+). [installation](https://cert-manager.io/docs/trust/trust-manager/installation/)
- **Metrics**: Prometheus metrics exposed by the operator, with optional ServiceMonitor for scraping. [v0.21.0](https://github.com/cert-manager/trust-manager/releases/tag/v0.21.0)
