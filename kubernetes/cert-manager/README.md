---
upstream: https://github.com/jetstack/cert-manager
last_updated: 2026-08-16
---

# cert-manager

Kubernetes-native certificate management: you declare the TLS certificates you need as custom resources and cert-manager issues and renews them automatically, without manual `certbot`/`openssl` workflows. Issuers are declared once as `Issuer`/`ClusterIssuer` resources and can point at ACME CAs (Let's Encrypt and friends, via HTTP-01 or DNS-01), an in-cluster or external CA, self-signing, Vault, or Venafi; `Certificate` resources then track issuance, renewal windows, private keys, keystores, and secret rotation, while `cainjector` injects the CA into service certificates and pod manifests as needed.

- Upstream repository: https://github.com/jetstack/cert-manager
- Documentation: https://cert-manager.io/docs/
- License: Apache-2.0
- API groups/versions: `cert-manager.io/v1`, `acme.cert-manager.io/v1`

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)

## Deployment on the homelab cluster

Managed with Flux in namespace `pki` from [`k8s-infrastructure/infrastructure/cert-manager/`](https://github.com/mmontes11/k8s-infrastructure/tree/main/infrastructure/cert-manager):

- Chart: [cert-manager](https://cert-manager.io/docs/installation/helm/) Helm chart pulled as OCI artifact from `oci://quay.io/jetstack/charts/cert-manager`, pinned at tag `v1.21.1` (`source.toolkit.fluxcd.io/v1` `OCIRepository` + `helm.toolkit.fluxcd.io/v2` `HelmRelease`).
- CRDs: the Helm chart is installed with `crds.enabled: false`; the CRDs are applied separately through a `Kustomization` pointing at the `cert-manager.crds.yaml` release asset (currently pinned to `v1.20.2`), so CRD upgrades are independent of the controller upgrade.
- Configuration: a `ControllerConfiguration` CR (`controller.config.cert-manager.io/v1alpha1`) sets `enableGatewayAPI: true` (Gateway API cert management); Prometheus scraping is enabled with a `ServiceMonitor`.
- Issuers: `ClusterIssuer/selfsigned` (bootstrap), `ClusterIssuer/homelab` (in-cluster root CA backing `Certificate/homelab-tls`), and Let's Encrypt staging + production `ClusterIssuer`s via a DuckDNS webhook (chart `cert-manager-webhook-duckdns`, credentials in `SealedSecret/duckdns`).
