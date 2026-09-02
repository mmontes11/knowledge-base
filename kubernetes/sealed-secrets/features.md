---
upstream: https://github.com/bitnami-labs/sealed-secrets
last_updated: 2026-08-18
---

# sealed-secrets — Features

Significant feature areas, each with a short explanation and the upstream [README](https://github.com/bitnami/sealed-secrets#readme) section covering it in depth (the in-repo `site/` docs tree redirects to the README).

## Sealing workflow

- **`kubeseal` CLI**: local binary that fetches the controller's public certificate at seal time and seals a JSON/YAML `Secret` definition into a `SealedSecret`; the output is safe for public repositories. Supports `--from-file`, `--cert`, `--controller-namespace`, `--raw` (experimental), and patching/updating of previously sealed files. [Usage](https://github.com/bitnami/sealed-secrets#usage)
- **Controller / operator**: watches for `SealedSecret`s and unseals them with the private key, creating or updating a same-named plain `Secret` in the target namespace; progress is reported via `status.conditions` (`Synced`) and `status.observedGeneration`. [Overview](https://github.com/bitnami/sealed-secrets#overview)
- **`SealedSecret`s as secret templates**: `spec.template` carries the created `Secret`'s metadata, `type`, `immutable` flag, and `data` keys expressed as Go templates evaluated against the decrypted values, so one sealed value can materialize several derived keys (plaintext `template.data` visible in the render context since v0.37.0, [PR #1940](https://github.com/bitnami/sealed-secrets/pull/1940)). [SealedSecrets as templates for secrets](https://github.com/bitnami/sealed-secrets#sealedsecrets-as-templates-for-secrets)

## Sealing scopes

- **`strict` (default)** — the target name **and namespace** are embedded in the ciphertext, pinning the sealed payload to an exact `Secret` location.
- **`namespace-wide` / `cluster-wide`** — via the `sealedsecrets.bitnami.com/namespace-wide: "true"` and `sealedsecrets.bitnami.com/cluster-wide: "true"` annotations (set with `kubeseal` flags), the payload may be unsealed under any name in the namespace, or in any namespace under any name. [Scopes](https://github.com/bitnami/sealed-secrets#scopes)

## Key management and rotation

- **Managed key pair**: on first start the controller generates an RSA key pair it self-renews, stored in a TLS `Secret` in the controller's namespace (default `sealed-secrets-key`); `kubeseal` fetches the current public certificate at seal time. [Public key / Certificate](https://github.com/bitnami/sealed-secrets#public-key--certificate)
- **Bring your own certificates**: pre-generated (e.g. HSM-backed) key pairs are consumed from TLS `Secret`s labeled `sealedsecrets.bitnami.com/sealed-secrets-key=active` in the controller's namespace. [docs/bring-your-own-certificates.md](https://github.com/bitnami/sealed-secrets/blob/main/docs/bring-your-own-certificates.md)
- **Sealing-key renewal and re-encryption**: keys can be rotated while older `SealedSecret`s remain decryptable (multi-key registry, precedence by certificate `NotBefore` / secret creation time), with documented early-renewal and manual re-encryption workflows. [Secret Rotation](https://github.com/bitnami/sealed-secrets#secret-rotation)
- **User secret rotation**: when a *sealed* secret must be invalidated (compromised plaintext), every affected `SealedSecret` is re-sealed with a new value; the controller's `/v1/verify` and `/v1/rotate` HTTP endpoints support this (rotate endpoint rate-limited since v0.39.0, [PR #1971](https://github.com/bitnami/sealed-secrets/pull/1971)). [User secret rotation](https://github.com/bitnami/sealed-secrets#user-secret-rotation)

## Namespacing and restricted environments

- **Scoped controller**: one controller instance can be limited to a subset of namespaces (`--additional-namespace`, comma-separated) with configurable unseal retries — one controller per tenant is the multi-tenancy pattern. [FAQ: one controller for a subset of namespaces](https://github.com/bitnami/sealed-secrets#how-to-use-one-controller-for-a-subset-of-namespaces)
- **Restricted (no-RBAC) installs**: the controller can run without creating RBAC/ServiceAccounts — it uses a provided ServiceAccount, expects pre-installed CRDs, and has documented resource limits; this mode is the basis for the GKE Warden guidance in [docs/GKE.md](https://github.com/bitnami/sealed-secrets/blob/main/docs/GKE.md). [Installation in Restricted Environments (No RBAC)](https://github.com/bitnami/sealed-secrets#installation-in-restricted-environments-no-rbac)

## Observability

- **Prometheus metrics** (`--enable-prometheus-metrics`): per-`SealedSecret` condition metrics and unseal-retry counters; per-secret label population is omittable via `--metrics-omit-secret-labels` (v0.39.0).
- **Helm chart integrations**: `metrics.prometheusRule.*` (default rule for out-of-sync `SealedSecret`s since v0.38.0), `metrics.serviceMonitor.*` for Prometheus Operator, and `metrics.dashboards.*` to deploy a Grafana dashboard `ConfigMap`. [chart values](https://github.com/bitnami/sealed-secrets/blob/main/helm/sealed-secrets/values.yaml)

## Installation and compatibility

- **Helm chart** at `https://bitnami.github.io/sealed-secrets` (chart series `helm-v2.x`, also published as OCI) with `install.crds` controlling CRD installation, and **kustomize/Carvel** manifests for non-Helm installs; controllers are signed (verifiable per [the docs](https://github.com/bitnami/sealed-secrets#how-to-verify-the-images)) and multi-arch: amd64, arm, arm64, ppc64le (since v0.38.0). [Installation](https://github.com/bitnami/sealed-secrets#installation), [Supported Versions](https://github.com/bitnami/sealed-secrets#supported-versions)
- **`kubeseal` distribution**: Linux tarballs, Homebrew, MacPorts, Nixpkgs, and build-from-source. [Kubeseal](https://github.com/bitnami/sealed-secrets#kubeseal)

## Security hardening

- v0.39.0: `/v1/verify` fixed so it can no longer be used as a decryption oracle ([PR #2019](https://github.com/bitnami/sealed-secrets/pull/2019)).
- v0.38.0: controller Pod security contexts default to the `restricted` Pod Security Standard ([PR #1981](https://github.com/bitnami/sealed-secrets/pull/1981)).
- Trust model: a sealed payload is useless without the controller's private key — it cannot be decrypted from a repo leak, cluster backup (absent the key `Secret`), or by anyone with cluster read access, including cluster admins. [Crypto details](https://github.com/bitnami/sealed-secrets#details-advanced)
