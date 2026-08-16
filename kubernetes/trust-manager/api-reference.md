---
upstream: https://github.com/cert-manager/trust-manager
last_updated: 2026-08-16
---

# trust-manager — API reference

The operator ships two custom resource kinds, both **cluster-scoped**: `Bundle` (the current API) and its successor `ClusterBundle` (v1alpha2), for which an automatic migration path is in progress.

| Kind | API group/version | Purpose | Upstream API docs |
| ---- | ----------------- | ------- | ----------------- |
| `Bundle` | `trust.cert-manager.io/v1alpha1` | Combines CA sources (ConfigMap, Secret, inline PEM, or the system default CAs) into a single trust bundle and writes it to target `ConfigMap`s/`Secret`s per namespace selector. | [docs](https://cert-manager.io/docs/trust/trust-manager/api-reference/) |
| `ClusterBundle` | `trust-manager.io/v1alpha2` | Successor API to `Bundle`: `defaultCAs`/`inLineCAs`/`sourceRefs` inputs and restructuring target with per-key entries in PEM/PKCS12 format and a required `namespaceSelector`. Intended to leave room for a future namespace-scoped `Bundle`. | [docs](https://cert-manager.io/docs/trust/trust-manager/api-reference/) |

Notes:

- Neither kind has short names; the `trust.cert-manager.io` vs `trust-manager.io` group disambiguates them.
- `ClusterBundle` v1alpha2 shape (per the CRD schema):
  - `spec.defaultCAs.provider` — `System` or `Disabled`; `spec.inLineCAs` — inline PEM; `spec.sourceRefs[]` — `kind` (`ConfigMap`/`Secret`) plus exactly one of `name` or `selector`, optional `key`.
  - `spec.target` — requires at least one entry. Supports `configMap` (metadata plus `data[]` entries, each `format` `PEM` or `PKCS12`, optional `password` and `profile` `LegacyRC2`/`LegacyDES`/`Modern2023`, default `LegacyDES`), `secret`, and `namespaceSelector` (required in v1alpha2; an empty `{}` matches all namespaces).
  - PKCS#12 is delivered via `configMap.data[].format`/secret entries; JKS support was removed in v1alpha2.
  - v1alpha1 → v1alpha2 API differences are documented upstream: [docs/alphav2-changes.md](https://github.com/cert-manager/trust-manager/blob/main/docs/alphav2-changes.md)
- CRDs are committed under [`deploy/crds/`](https://github.com/cert-manager/trust-manager/tree/main/deploy/crds) (`trust.cert-manager.io_bundles.yaml`, `trust-manager.io_clusterbundles.yaml`), but upstream marks that directory as reference/dev/test-only — install CRDs through the Helm chart instead. [deploy/crds/README.md](https://github.com/cert-manager/trust-manager/blob/main/deploy/crds/README.md)
- Field-level documentation is intentionally not duplicated here; follow the upstream API reference link.
