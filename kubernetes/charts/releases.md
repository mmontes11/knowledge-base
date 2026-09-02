---
upstream: https://github.com/mmontes11/charts
last_updated: 2026-08-23
---

# charts — releases

Latest 10 official releases (per-chart tags), newest first. Releases are created automatically by [chart-releaser](https://github.com/helm/chart-releaser) in the [Helm workflow](https://github.com/mmontes11/charts/blob/main/.github/workflows/helm.yml) as tags named `<chart>-<version>` (e.g. `duckdns-0.3.0`) and the release body is the one-line chart description; highlights below are taken from the commit each tag points to.

## tenant-0.2.0 — 2026-05-16

[Release page](https://github.com/mmontes11/charts/releases/tag/tenant-0.2.0)

- **GA Flux CRDs**: the `GitRepository` and `Kustomization` templates now target `source.toolkit.fluxcd.io/v1` and `kustomize.toolkit.fluxcd.io/v1` instead of the beta API versions used in 0.1.0 ([gitrepository.yaml](https://github.com/mmontes11/charts/blob/tenant-0.2.0/deploy/charts/tenant/templates/gitrepository.yaml), [kustomization.yaml](https://github.com/mmontes11/charts/blob/tenant-0.2.0/deploy/charts/tenant/templates/kustomization.yaml)); clusters on older Flux releases need to upgrade Flux before adopting this chart version.

## photoprism-0.14.0 — 2026-01-25

[Release page](https://github.com/mmontes11/charts/releases/tag/photoprism-0.14.0)

- Runtime class support: `runtimeClassName` can be set on the main Deployment and on the batch CronJobs ([commit 24f9ac9](https://github.com/mmontes11/charts/commit/24f9ac9927fcd404d66a5032614932c889da4bf5)).

## photoprism-0.13.0 — 2025-12-18

[Release page](https://github.com/mmontes11/charts/releases/tag/photoprism-0.13.0)

- The AI "vision" configuration can now be sourced from any existing volume (`vision.configVolume`) instead of only the chart-generated ConfigMap ([commit 460c8d9](https://github.com/mmontes11/charts/commit/460c8d9f69464342a8373244c6f2e611be520dc2)).

## photoprism-0.12.0 — 2025-12-17

[Release page](https://github.com/mmontes11/charts/releases/tag/photoprism-0.12.0)

- **AI/vision batch jobs**: `batch.jobs[]` (off by default) renders one CronJob per job — by default `vision-labels` and `vision-caption` running Photoprism `vision run` with the bundled vision config aimed at Ollama (`http://ollama:11434/api/generate`) — replacing the previous single `sync` job; each job has its own schedule, suspend flag, command/args, plus shared batch-level resources/scheduling values. A new `vision` values block (off by default) sources the vision config file ([commit 0b5e12b](https://github.com/mmontes11/charts/commit/0b5e12b66b24c2b608073106cbcc3d743c92f170)).

## photoprism-0.11.0 — 2025-08-25

[Release page](https://github.com/mmontes11/charts/releases/tag/photoprism-0.11.0)

- Service annotations and pod tolerations became configurable (the commit touches both the Deployment and the CronJob templates).
- ⚠️ **Default change: video transcoding is disabled by default** — enable it explicitly if a 0.10 install relied on transcoding running ([commit 3fa3a09](https://github.com/mmontes11/charts/commit/3fa3a09b899db00cee66b325aedf8fa47f0781b1)).

## photoprism-0.10.0 — 2024-08-03

[Release page](https://github.com/mmontes11/charts/releases/tag/photoprism-0.10.0)

- `priorityClassName` support for the main Deployment ([commit 384320f](https://github.com/mmontes11/charts/commit/384320f4ed593b9a059632c9b189fc5631600c8e)).

## photoprism-0.9.0 — 2024-07-04

[Release page](https://github.com/mmontes11/charts/releases/tag/photoprism-0.9.0)

- **Video transcoding support** (ffmpeg-based) was introduced, initially enabled by default ([commit 2e0b6fd](https://github.com/mmontes11/charts/commit/2e0b6fdf6efc80fefb195c9fde822c094a15f290)).

## photoprism-0.8.0 — 2024-07-04

[Release page](https://github.com/mmontes11/charts/releases/tag/photoprism-0.8.0)

- Fixes to the Photoprism liveness/readiness probes and the resource values applied to the Deployment ([commit f89bfa1](https://github.com/mmontes11/charts/commit/f89bfa1c57d2bdd52dae0d11c4f1335eaa27938a)).

## photoprism-0.7.0 — 2024-07-04

[Release page](https://github.com/mmontes11/charts/releases/tag/photoprism-0.7.0)

- The chart stopped setting default resource requests/limits on the Deployment ([commit d6a26c7](https://github.com/mmontes11/charts/commit/d6a26c7e594bd585e7223a902df77b79a197915b)).

## photoprism-0.6.0 — 2024-07-04

[Release page](https://github.com/mmontes11/charts/releases/tag/photoprism-0.6.0)

- Configurable liveness and readiness probes for the Photoprism Deployment ([commit f78a248](https://github.com/mmontes11/charts/commit/f78a2489955df78346d88ec7b22ba564b9e4f315)).

### Conventions

- Releases are **per chart**: `duckdns-0.3.0` (2023-02-23), `cert-manager-webhook-duckdns-v1.2.3` (2024-06-29), etc. are independent of each other; the [full list](https://github.com/mmontes11/charts/releases) has ~20 additional per-chart tags, most from early 2023.
- Lint gate: [chart-testing](https://github.com/helm/chart-testing) with `target-branch: main` and `check-version-increment: false` ([ct.yml](https://github.com/mmontes11/charts/blob/main/.github/config/ct.yml)); the publish step uses `skip_existing: true`, so pushing a chart with an unchanged `Chart.yaml` version does not cut a new release.
