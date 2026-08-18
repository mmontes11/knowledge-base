---
upstream: https://github.com/jetstack/cert-manager
last_updated: 2026-08-16
---

# cert-manager — features

Key feature areas, with links to the official docs (<https://cert-manager.io/docs/>).

## Issuer types

- **ACME** (Let's Encrypt and other ACME CAs, including private CAs): HTTP-01 and DNS-01 challenges, External Account Binding, per-zone solver selectors — [configuration docs](https://cert-manager.io/docs/configuration/acme/).
- **CA issuer**: signs against an in-cluster root or intermediate CA stored in a Secret — [docs](https://cert-manager.io/docs/configuration/ca/).
- **selfSigned**: self-signs each certificate; useful for bootstrapping a local CA hierarchy — [docs](https://cert-manager.io/docs/configuration/selfsigned/).
- **Vault**: issues from a HashiCorp Vault instance; supports AppRole, Kubernetes, and (since v1.21) AWS IAM authentication — [docs](https://cert-manager.io/docs/configuration/vault/).
- **Venafi**: integrates with Venafi (TPP, NGTS, cloud) smart-card/PKI platforms — [docs](https://cert-manager.io/docs/configuration/venafi/).
- Issuer concept overview: [docs](https://cert-manager.io/docs/concepts/issuer/).

## ACME solving

- HTTP-01 with Ingress or dedicated solver pods (external load balancer mode) — [docs](https://cert-manager.io/docs/configuration/acme/http01/).
- DNS-01 with 40+ built-in providers (Route53, Cloudflare, Azure — including private zones since v1.20 — Akamai, Google, DigitalOcean, RFC2136, acme-dns, Webhook, …) — [docs](https://cert-manager.io/docs/configuration/acme/dns01/).
- **Challenge webhook** plugin API for custom DNS-01 providers — [concept docs](https://cert-manager.io/docs/concepts/webhook/) and the [webhook provider guide](https://cert-manager.io/docs/configuration/acme/dns01/webhook/).
- Order/challenge lifecycle concepts: [docs](https://cert-manager.io/docs/concepts/acme-orders-challenges/).
- v1.21 additions: ACME Renewal Information (RFC 9773) behind the `ACMEUseARI` feature gate, and `waitInsteadOfSelfCheck` for split-horizon DNS.

## Gateway API & ingress integration

- **Gateway API**: manages certificates referenced by `Gateway`/`GatewayClass`/listener TLS config, including per-protocol listeners (since v1.20: `parentRef` overrides, `ignore-tls-listeners` annotation, alpha `ListenerSet` support) — [docs](https://cert-manager.io/docs/usage/gateway/).
- **Ingress**: classic `cert-manager.io/cluster-issuer` annotation flow — [docs](https://cert-manager.io/docs/usage/ingress/).
- Istio CSR support for ambient mesh workloads — [docs](https://cert-manager.io/docs/usage/istio-csr/).

## Certificate lifecycle

- **Certificate**: declarative issuance with automatic renewal (`renewBefore` / `renewBeforePercentage`, plus `renewalPolicies` since v1.21), private-key algorithm/size/rotation, keystore output (JKS, PKCS#12 with legacy + Modern2026 FIPS profiles), additional output formats, SANs, OtherNames (Beta), and Secret templating — [usage docs](https://cert-manager.io/docs/usage/certificate/), [concept docs](https://cert-manager.io/docs/concepts/certificate/).
- **CertificateRequest**: ad-hoc, one-shot signing against an issuer (no renewal) — [usage docs](https://cert-manager.io/docs/usage/certificaterequest/), [concept docs](https://cert-manager.io/docs/concepts/certificaterequest/).
- **cainjector**: watches Secrets and pod/manifest references to inject the relevant CA certificate, and manages CA chains (e.g. re-signs issued certs when the CA rotates) — [concept docs](https://cert-manager.io/docs/concepts/ca-injector/); configured via `CAInjectorConfiguration`.

## Deployment & operations

- Install via Helm chart (also published as the OCI artifact `oci://quay.io/jetstack/charts/cert-manager`) or the standalone `cert-manager.crds.yaml` + manifests from each release — [installation docs](https://cert-manager.io/docs/installation/), [Helm docs](https://cert-manager.io/docs/installation/helm/).
- Per-component configuration APIs (`v1alpha1`): `ControllerConfiguration` (e.g. `enableGatewayAPI` / `gatewayAPI.enabled`), `WebhookConfiguration`, `CAInjectorConfiguration` — see the [API docs page](https://cert-manager.io/docs/reference/api-docs/).
- Observability: Prometheus metrics on a dedicated port, with native `ServiceMonitor`/`PodMonitor` support in the Helm chart; configurable NetworkPolicies, `extraContainers` sidecars, `runtimeClassName`, and `startupapicheck` job controls (Helm values).

## Deployment on the homelab cluster

Flux-managed in the `pki` namespace from `k8s-infrastructure/infrastructure/cert-manager/` (chart `v1.21.1` from the OCI repository, CRDs applied separately via `Kustomization`, `crds.enabled: false` in the chart):

- `ControllerConfiguration` CR enables Gateway API cert management (`enableGatewayAPI: true`); Prometheus + `ServiceMonitor` enabled.
- `ClusterIssuer/selfsigned`: bootstraps the in-cluster CA (`CA` issuer mode, `selfSigned`).
- `ClusterIssuer/homelab` + `Certificate/homelab-tls`: the homelab root CA (365-day certificate, auto-renewing).
- Let's Encrypt `staging` and `production` `ClusterIssuer`s backed by the DuckDNS webhook (`cert-manager-webhook-duckdns` chart, `SealedSecret/duckdns` credentials) for DNS-01.
