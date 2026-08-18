---
upstream: https://github.com/cert-manager/trust-manager
last_updated: 2026-08-16
---

# trust-manager

Kubernetes operator for managing TLS trust bundles declaratively: you define which certificate authorities to trust (the system default CAs, ConfigMaps, Secrets, or inline PEM) and where the merged bundle should be distributed, and trust-manager keeps the target `ConfigMap`s and `Secrets` in sync across all selected namespaces. It is part of the cert-manager project family, and all clusters that use cert-manager or any cluster-issued PKI need a managed trust bundle so workloads can verify TLS certificates.

- Upstream repository: https://github.com/cert-manager/trust-manager
- Documentation: [usage](https://cert-manager.io/docs/trust/trust-manager/), [installation](https://cert-manager.io/docs/trust/trust-manager/installation/), [API reference](https://cert-manager.io/docs/trust/trust-manager/api-reference/) on cert-manager.io
- License: Apache-2.0
- API groups/versions: `Bundle` under `trust.cert-manager.io/v1alpha1` and `ClusterBundle` under `trust-manager.io/v1alpha2`, both cluster-scoped

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
