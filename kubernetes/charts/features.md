---
upstream: https://github.com/mmontes11/charts
last_updated: 2026-08-23
---

# charts — features

The repository's function splits into (a) how the chart repository itself is built and delivered, and (b) what the individual charts do. Chart-level configuration and rendered resources are in [api-reference.md](api-reference.md); version history is in [releases.md](releases.md).

## Chart publishing pipeline

Changes under `deploy/charts/` are gated by [chart-testing](https://github.com/helm/chart-testing) lint on push and PR ([ct config](https://github.com/mmontes11/charts/blob/main/.github/config/ct.yml)), also runnable locally via `make helm-lint` with the pinned `quay.io/helmpack/chart-testing:v3.5.0` image ([Makefile](https://github.com/mmontes11/charts/blob/main/Makefile)). After a green push to `main`, the [Helm workflow](https://github.com/mmontes11/charts/blob/main/.github/workflows/helm.yml) runs [chart-releaser](https://github.com/helm/chart-releaser) (`helm/chart-releaser-action`), which packages each changed chart into a `.tgz`, cuts a `<chart>-<version>` GitHub release, and pushes the repository index to the `gh-pages` branch — that branch is what GitHub Pages serves at https://mmontes11.github.io/charts. The repository is also indexed by [Artifact Hub](https://artifacthub.io/packages/search?repo=mmontes) under the name `mmontes`. Net effect: the single source of truth for "what is published" is `deploy/charts/` on `main`; there are no manual chart uploads.

## DuckDNS domain auto-renewal — chart `duckdns`

`linuxserver/duckdns` keeps DuckDNS subdomain DNS records fresh on a schedule. The chart takes a list of subdomains, a timezone, and the name of an existing Secret holding the DuckDNS API token, and runs 2 replicas with required per-host pod anti-affinity so renewals keep working when a node drains. This is the renewal engine that the cert-manager webhook below relies on for the record's existence. Source: [deploy/charts/duckdns](https://github.com/mmontes11/charts/tree/main/deploy/charts/duckdns).

## ACME DNS-01 via DuckDNS — chart `cert-manager-webhook-duckdns`

Let's Encrypt DNS-01 challenges normally need write access to a DNS provider. This chart installs a cert-manager webhook extension that instead creates a *fresh* DuckDNS subdomain, points it at the validated target, and lets the challenge pass — i.e. the cluster can issue public certificates without any DNS-provider credentials. It ships the full serving PKI (self-signed bootstrap `Issuer`, root CA, serving `Certificate`, `APIService` under group `acme.duckdns.org`) and can generate ready-to-use Let's Encrypt staging/production `ClusterIssuer`s whose solvers reference the webhook and the DuckDNS token Secret — both are off by default in `main`'s values (`clusterIssuer.{staging,production}.create: false`). The implementation is the community project [ebrianne/cert-manager-webhook-duckdns](https://github.com/ebrianne/cert-manager-webhook-duckdns); this repository hosts the chart consumed by the owner's infra (HelmRepository `mmontes`, `cert-manager-webhook-duckdns` v1.2.3 in `mmontes11/k8s-infrastructure`). Source: [deploy/charts/cert-manager-webhook-duckdns](https://github.com/mmontes11/charts/tree/main/deploy/charts/cert-manager-webhook-duckdns).

## Flux tenant bootstrap — chart `tenant`

On a cluster already running Flux v2, this chart is a one-shot tenant onboarding: it creates a ServiceAccount in `flux-system` bound (cluster-wide) to the `edit` ClusterRole and the Flux CRD controller ClusterRole, then a `GitRepository` and a `Kustomization` that reconcile a caller-supplied git repository path with `prune` and `wait` on. `tenant-0.2.0` migrated the manifests to the GA `v1` API versions of both CRDs. It is the lightest-weight "add one more git repo to Flux" mechanism in the catalog. Source: [deploy/charts/tenant](https://github.com/mmontes11/charts/tree/main/deploy/charts/tenant).

## Photoprism with AI batch jobs — chart `photoprism`

The most developed chart in the repository (v0.14.0 on `main`) covers the whole photoprism operational surface:

- **Core**: Deployment with liveness/readiness `httpGet` probes and no imposed resource defaults; `ClusterIP:80` Service; env via chart-generated ConfigMaps plus an optional existing `secretRef`; database DSN from an existing Secret (`photoprism-dsn`, MySQL driver by default). Storage is **raw-volume based** (default: three NFS volumes — read-only `originals`, `import`, `storage` — on `persistence.nas`; `emptyDir` fallback when disabled), not the PVC pattern.
- **AI storage and config**: `vision` (off by default) mounts the generated vision-config ConfigMap (or, since 0.13, any existing volume via `vision.configVolume`) at `/photoprism/storage/config/vision.yml`; the bundled config defines `labels` and `caption` models served by Ollama.
- **AI batch processing**: `batch.jobs[]` (off by default) renders one CronJob per job — by default `vision-labels` and `vision-caption` running `photoprism vision run -m … --count 1 --force` on a `*/5 * * * *` schedule — with per-job `suspend`, shared batch-level resources/scheduling, and `runtimeClassName`/`priorityClassName` on both the Deployment and the CronJobs (0.14).
- **Video transcoding** (since 0.9): **disabled by default since 0.11** — a deliberate default change per the [release notes](https://github.com/mmontes11/charts/releases/tag/photoprism-0.11.0).
- **Routing**: optional Traefik `IngressRoute` (host + `websecure` entrypoints) and Gateway API `HTTPRoute` (host + `gatewayRef`), plus an optional PodDisruptionBudget.

Sources: [Chart.yaml](https://github.com/mmontes11/charts/blob/main/deploy/charts/photoprism/Chart.yaml), [values.yaml](https://github.com/mmontes11/charts/blob/main/deploy/charts/photoprism/values.yaml) and [templates/](https://github.com/mmontes11/charts/tree/main/deploy/charts/photoprism/templates).

## Homelab data services and demo charts

`redis` and `mongodb` are described in their release notes as "with metrics compatible with ARM" — single-node data services aimed at ARM homelab hardware. `mariadb` is a single-node MariaDB flavor. The rest are demo/integration experiments: `bankroach` (Go CRUD app on CockroachDB), `cockroachdb-operator`, `echoperator` (echo-CRD demo operator), `github-explorer` (React + Apollo GraphQL UI), and `iot` (general-purpose IoT platform). Catalog details with sources: [README.md](README.md).
