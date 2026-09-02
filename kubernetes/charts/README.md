---
upstream: https://github.com/mmontes11/charts
last_updated: 2026-08-23
---

# charts

A Helm chart repository that publishes a small catalog of application charts as the `mmontes` Helm repository at https://mmontes11.github.io/charts. Chart sources live under `deploy/charts/<name>/`; a push to `main` touching that path runs [chart-testing](https://github.com/helm/chart-testing) lint and, when it passes, [chart-releaser](https://github.com/helm/chart-releaser) packages the changed charts, cuts a `<chart>-<version>` GitHub release, and refreshes the repository index on the `gh-pages` branch that serves the index URL ([workflow](https://github.com/mmontes11/charts/blob/main/.github/workflows/helm.yml)). The repository is also listed on [Artifact Hub](https://artifacthub.io/packages/search?repo=mmontes). The charts target homelab-style clusters; `duckdns` and `cert-manager-webhook-duckdns` are consumed from this repository by the owner's GitOps infrastructure via Flux `HelmRelease`s (HelmRepository `mmontes` → `https://mmontes11.github.io/charts`, see `mmontes11/k8s-infrastructure`).

- Upstream repository: https://github.com/mmontes11/charts
- Helm repository index: https://mmontes11.github.io/charts (`helm repo add mmontes https://mmontes11.github.io/charts`)
- Artifact Hub: https://artifacthub.io/packages/search?repo=mmontes
- License: MIT ([LICENSE](https://github.com/mmontes11/charts/blob/main/LICENSE))

Chart catalog (12 charts, sources under [`deploy/charts/`](https://github.com/mmontes11/charts/tree/main/deploy/charts)):

| Chart | Latest release | Purpose |
| ----- | -------------- | ------- |
| [bankroach](https://github.com/mmontes11/charts/tree/main/deploy/charts/bankroach) | [0.1.1](https://github.com/mmontes11/charts/releases/tag/bankroach-0.1.1) | Simple CRUD application using CockroachDB and Go. |
| [cert-manager-webhook-duckdns](https://github.com/mmontes11/charts/tree/main/deploy/charts/cert-manager-webhook-duckdns) | [v1.2.3](https://github.com/mmontes11/charts/releases/tag/cert-manager-webhook-duckdns-v1.2.3) | cert-manager webhook that solves ACME DNS-01 challenges via the DuckDNS API. |
| [cockroachdb-operator](https://github.com/mmontes11/charts/tree/main/deploy/charts/cockroachdb-operator) | [0.1.0](https://github.com/mmontes11/charts/releases/tag/cockroachdb-operator-0.1.0) | CockroachDB operator. |
| [duckdns](https://github.com/mmontes11/charts/tree/main/deploy/charts/duckdns) | [0.3.0](https://github.com/mmontes11/charts/releases/tag/duckdns-0.3.0) | DuckDNS domain auto-renewal. |
| [echoperator](https://github.com/mmontes11/charts/tree/main/deploy/charts/echoperator) | [0.0.2](https://github.com/mmontes11/charts/releases/tag/echoperator-0.0.2) | Simple Kubernetes operator for handling echo CRDs. |
| [github-explorer](https://github.com/mmontes11/charts/tree/main/deploy/charts/github-explorer) | [0.2.3](https://github.com/mmontes11/charts/releases/tag/github-explorer-0.2.3) | React UI for exploring GitHub using Apollo GraphQL. |
| [iot](https://github.com/mmontes11/charts/tree/main/deploy/charts/iot) | [0.3.2](https://github.com/mmontes11/charts/releases/tag/iot-0.3.2) | General purpose Internet of Things platform. |
| [mariadb](https://github.com/mmontes11/charts/tree/main/deploy/charts/mariadb) | [0.3.0](https://github.com/mmontes11/charts/releases/tag/mariadb-0.3.0) | MariaDB database (single-node homelab flavor). |
| [mongodb](https://github.com/mmontes11/charts/tree/main/deploy/charts/mongodb) | [0.5.0](https://github.com/mmontes11/charts/releases/tag/mongodb-0.5.0) | MongoDB "with metrics compatible with ARM" (per [release notes](https://github.com/mmontes11/charts/releases/tag/mongodb-0.5.0)). |
| [photoprism](https://github.com/mmontes11/charts/tree/main/deploy/charts/photoprism) | [0.14.0](https://github.com/mmontes11/charts/releases/tag/photoprism-0.14.0) | Photoprism media server with AI "vision" batch jobs, probes, and Traefik/Gateway API routing. |
| [redis](https://github.com/mmontes11/charts/tree/main/deploy/charts/redis) | [0.4.0](https://github.com/mmontes11/charts/releases/tag/redis-0.4.0) | Redis "with metrics compatible with ARM" (per [release notes](https://github.com/mmontes11/charts/releases/tag/redis-0.4.0)). |
| [tenant](https://github.com/mmontes11/charts/tree/main/deploy/charts/tenant) | [0.2.0](https://github.com/mmontes11/charts/releases/tag/tenant-0.2.0) | Bootstraps a Flux tenant: `GitRepository` + `Kustomization` (GA `v1` CRD versions) plus RBAC in `flux-system`. |

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
