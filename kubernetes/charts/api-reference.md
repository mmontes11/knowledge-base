---
upstream: https://github.com/mmontes11/charts
last_updated: 2026-08-23
---

# charts — API reference

`mmontes11/charts` is a **Helm chart repository**, so there is no single API: the API surface is the catalog of chart packages — each chart's values, the Kubernetes resources it renders, and the CRDs or webhooks it creates. Sources live under [`deploy/charts/<name>/`](https://github.com/mmontes11/charts/tree/main/deploy/charts); charts are published to [https://mmontes11.github.io/charts](https://mmontes11.github.io/charts) under the repository name `mmontes` and indexed on [Artifact Hub](https://artifacthub.io/packages/search?repo=mmontes).

Detailed surfaces are given for the charts that are actively deployed (`duckdns`, `cert-manager-webhook-duckdns`, `tenant`, `photoprism`); the remaining charts are catalogued in [README.md](README.md).

## duckdns

Chart `mmontes/duckdns` ([Chart.yaml](https://github.com/mmontes11/charts/blob/main/deploy/charts/duckdns/Chart.yaml), v0.3.0 on `main`), appVersion `0.1.0`. Runs `linuxserver/duckdns`, which periodically updates the DNS records for the configured DuckDNS subdomains. The DuckDNS token is expected to be provided out-of-band in an existing Secret.

Rendered resources (from [templates/](https://github.com/mmontes11/charts/tree/main/deploy/charts/duckdns/templates)):

| Kind | apiVersion | Notes |
| ---- | ---------- | ----- |
| `ConfigMap` | `v1` | `LOG_FILE=true`, `PGID=1000`, `PUID=1000`, `SUBDOMAINS` (comma-joined list), `TZ` ([configmap.yml](https://github.com/mmontes11/charts/blob/main/deploy/charts/duckdns/templates/configmap.yml)). |
| `Deployment` | `apps/v1` | Image `linuxserver/duckdns:73eeb4c2-ls96` (default), env from the ConfigMap plus the token Secret via `envFrom` ([deployment.yml](https://github.com/mmontes11/charts/blob/main/deploy/charts/duckdns/templates/deployment.yml)). |

Values ([values.yaml](https://github.com/mmontes11/charts/blob/main/deploy/charts/duckdns/values.yaml)):

| Value | Default | Purpose |
| ----- | ------- | ------- |
| `subdomains` | `[example.domain.org]` | DuckDNS subdomains to renew, comma-joined into `SUBDOMAINS`. |
| `timezone` | `Europe/Madrid` | `TZ` for the container. |
| `secretRef.name` | `duckdns` | Existing Secret carrying the DuckDNS token (env-merged via `secretRef`). |
| `replicas` | `2` | Pod count; required pod anti-affinity places them on distinct nodes. |
| `image.repository` / `image.tag` / `image.pullPolicy` | `linuxserver/duckdns` / `73eeb4c2-ls96` / `IfNotPresent` | Container image. |
| `nameOverride` / `fullnameOverride` | `""` | Naming overrides. |
| `resources` | `{}` | Requests/limits. |
| `nodeSelector` / `affinity` | `{}` / `requiredDuringSchedulingIgnoredDuringExecution` pod anti-affinity (selector `app.kubernetes.io/name in [duckdns]`, topology key `kubernetes.io/hostname`) | Scheduling. |

## cert-manager-webhook-duckdns

Chart `mmontes/cert-manager-webhook-duckdns` ([Chart.yaml](https://github.com/mmontes11/charts/blob/main/deploy/charts/cert-manager-webhook-duckdns/Chart.yaml), v1.2.3 on `main`; Chart `apiVersion` is `v1`, i.e. legacy chart format). A [cert-manager](https://cert-manager.io) webhook extension that solves ACME **DNS-01** challenges by creating DuckDNS subdomains via the DuckDNS API. It is the community project [ebrianne/cert-manager-webhook-duckdns](https://github.com/ebrianne/cert-manager-webhook-duckdns) as declared in the chart's `home`/`sources` fields. The chart's [README](https://github.com/mmontes11/charts/blob/main/deploy/charts/cert-manager-webhook-duckdns/README.md) states requirements of cert-manager, Kubernetes `>=1.18`, and Helm `>=3`.

Rendered resources (from [templates/](https://github.com/mmontes11/charts/tree/main/deploy/charts/cert-manager-webhook-duckdns/templates)):

| Kind | apiVersion | Notes |
| ---- | ---------- | ----- |
| `Deployment` | `apps/v1` | Webhook; image `ebrianne/cert-manager-webhook-duckdns:<tag>` (default `v1.2.3`, `pullPolicy: IfNotPresent`), args `--tls-cert-file`/`--tls-private-key-file` (cert volume mounted from the serving-certificate Secret at `/tls`) plus `--v=<logLevel>`; env `GROUP_NAME`; HTTPS `GET /healthz` liveness + readiness on port 443; `serviceAccountName` = chart fullname ([deployment.yaml](https://github.com/mmontes11/charts/blob/main/deploy/charts/cert-manager-webhook-duckdns/templates/deployment.yaml)). |
| `Service` | `v1` | `ClusterIP:443` fronting the webhook. |
| `APIService` | `apiregistration.k8s.io/v1` | `v1alpha1.<groupName>` (default group `acme.duckdns.org`) with the `cert-manager.io/inject-ca-from` annotation ([apiservice.yaml](https://github.com/mmontes11/charts/blob/main/deploy/charts/cert-manager-webhook-duckdns/templates/apiservice.yaml)). |
| `Issuer` (self-signed) + `Issuer` (CA) | `cert-manager.io/v1` | Bootstraps a 5-year root CA used to sign the webhook's serving certificate ([pki.yaml](https://github.com/mmontes11/charts/blob/main/deploy/charts/cert-manager-webhook-duckdns/templates/pki.yaml)). |
| `Certificate` (root CA) + `Certificate` (serving) | `cert-manager.io/v1` | Serving cert for the webhook Service DNS names; 1-year duration. |
| `ServiceAccount` + `RBAC` | `v1` / `rbac.authorization.k8s.io/v1` | ServiceAccount, `:domain-solver` ClusterRole (verb `create` on `<groupName>` resources) and the `:auth-delegator`, `:webhook-authentication-reader` (kube-system) and `:secret-reader` bindings — **all created in `certManager.namespace`** ([rbac.yaml](https://github.com/mmontes11/charts/blob/main/deploy/charts/cert-manager-webhook-duckdns/templates/rbac.yaml)). |
| `Secret` | `v1` | `Opaque` with `data.token` = base64(`duckdns.token`); rendered only when `secret.existingSecret` is `false`; the `secretName` helper falls back to `fullname` when `secret.existingSecretName` is empty ([secret.yaml](https://github.com/mmontes11/charts/blob/main/deploy/charts/cert-manager-webhook-duckdns/templates/secret.yaml)). |
| `ClusterIssuer` (production) | `cert-manager.io/v1` | Optional (`.create` default **`false`** in `main`'s values): Let's Encrypt ACME issuer with a DNS-01 `webhook` solver (`groupName`, `solverName: duckdns`, `apiTokenSecretRef`); `preferredChain` `ISRG Root X1` ([production.cluster-issuer.yaml](https://github.com/mmontes11/charts/blob/main/deploy/charts/cert-manager-webhook-duckdns/templates/production.cluster-issuer.yaml)). |
| `ClusterIssuer` (staging) | `cert-manager.io/v1` | Optional; off by default (`.create` default `false`). |

Values ([values.yaml](https://github.com/mmontes11/charts/blob/main/deploy/charts/cert-manager-webhook-duckdns/values.yaml)) — the fields an operator sets:

| Value | Default | Purpose |
| ----- | ------- | ------- |
| `groupName` | `acme.duckdns.org` | API group the webhook registers (must match `spec.dns01.webhook.groupName` in Issuer solvers). |
| `logLevel` | `2` | Rendered as the webhook's `--v=<logLevel>` argument. |
| `duckdns.token` | `""` | DuckDNS API token written to the generated Secret. |
| `secret.existingSecret` / `secret.existingSecretName` | `false` / `""` | Reuse an existing token Secret (by name) instead of the chart rendering one. |
| `clusterIssuer.email` | `name@example.com` | ACME account email shared by both generated issuers. |
| `clusterIssuer.production.create` / `clusterIssuer.staging.create` | `false` / `false` | Whether each `ClusterIssuer` is rendered. |
| `certManager.namespace` / `certManager.serviceAccountName` | `cert-manager` / `cert-manager` | Namespace where the ServiceAccount and RBAC are created, and the cert-manager ServiceAccount the `domain-solver` ClusterRole is bound to. |
| `service.type` / `service.port` | `ClusterIP` / `443` | Front Service for the webhook. |
| `image.repository` / `image.tag` / `image.pullPolicy` | `ebrianne/cert-manager-webhook-duckdns` / `v1.2.3` / `IfNotPresent` | Webhook image. `image.pullSecret` is an additional optional key. |
| `resources` / `nodeSelector` / `tolerations` / `affinity` | `{}` / `{}` / `[]` / `{}` | Scheduling knobs for the webhook Deployment. |
| `nameOverride` / `fullnameOverride` | `""` | Naming overrides. |

Note: the Deployment lands in the release namespace but runs as the ServiceAccount the chart creates in `certManager.namespace`, so the release must be installed **into the `cert-manager` namespace** (or point `certManager.namespace` at the release namespace).

## tenant

Chart `mmontes/tenant` ([Chart.yaml](https://github.com/mmontes11/charts/blob/main/deploy/charts/tenant/Chart.yaml), v0.2.0 on `main`, appVersion `1.16.0`), described as "Helm chart for bootstrapping a tenant". It installs a minimal, self-contained on-surface for one Git repository in an existing [Flux v2](https://fluxcd.io) cluster, entirely in the `flux-system` namespace ([templates/](https://github.com/mmontes11/charts/tree/main/deploy/charts/tenant/templates)):

| Kind | apiVersion | Notes |
| ---- | ---------- | ----- |
| `ServiceAccount` | `v1` | Actor for the tenant's `Kustomization`. |
| `ClusterRoleBinding` `<name>-edit` | `rbac.authorization.k8s.io/v1` | Binds ClusterRole `edit` to the ServiceAccount ([clusterrolebinding.yaml](https://github.com/mmontes11/charts/blob/main/deploy/charts/tenant/templates/clusterrolebinding.yaml)). |
| `ClusterRoleBinding` `<name>-edit-flux-crds` | `rbac.authorization.k8s.io/v1` | Binds ClusterRole `crd-controller-flux-system` (Flux CRD management) to the ServiceAccount. |
| `GitRepository` | `source.toolkit.fluxcd.io/v1` | `url` and `ref.branch` are **required**; `secretRef.name` default `flux-system`; interval `5m` ([gitrepository.yaml](https://github.com/mmontes11/charts/blob/main/deploy/charts/tenant/templates/gitrepository.yaml)). |
| `Kustomization` | `kustomize.toolkit.fluxcd.io/v1` | `path` is **required**; `prune: true`, `wait: true`, `timeout: 5m`; `serviceAccountName` is the chart's ServiceAccount ([kustomization.yaml](https://github.com/mmontes11/charts/blob/main/deploy/charts/tenant/templates/kustomization.yaml)). |

Values ([values.yaml](https://github.com/mmontes11/charts/blob/main/deploy/charts/tenant/values.yaml)):

| Value | Default | Purpose |
| ----- | ------- | ------- |
| `repo.url` / `repo.branch` / `repo.path` | `""` (required at install) | Tenant git repository and in-repo path reconciled by Flux. |
| `repo.secretName` | `flux-system` | Existing Secret with git credentials for the `GitRepository`. |
| `nameOverride` / `fullnameOverride` | `""` | Naming overrides. |

v0.2.0 is the GA-CRD migration (tag note "Updated tenant with GA flux CRDs", see [releases.md](releases.md)): templates now use `source.toolkit.fluxcd.io/v1` and `kustomize.toolkit.fluxcd.io/v1`, so the cluster must run a Flux release that serves the `v1` API versions of both CRDs; `0.1.0` used the beta API versions.

## photoprism

Chart `mmontes/photoprism` ([Chart.yaml](https://github.com/mmontes11/charts/blob/main/deploy/charts/photoprism/Chart.yaml), v0.14.0 on `main`, appVersion `251130` — a Photoprism release identifier; chart keywords include `ai` and `ollama`). Renders, from [templates/](https://github.com/mmontes11/charts/tree/main/deploy/charts/photoprism/templates):

| Kind | apiVersion | Notes |
| ---- | ---------- | ----- |
| `Deployment` | `apps/v1` | Image `image.repository:image.tag or appVersion` (default `photoprism/photoprism:251130`); env from the chart ConfigMaps plus optional `secretRef`; `PHOTOPRISM_DATABASE_DSN` from `database.dsnSecretKeyRef`; liveness/readiness `httpGet` on `/` port `http` (2342); `runtimeClassName`/`priorityClassName` always rendered (empty by default); nodeSelector/affinity/tolerations optional ([deployment.yml](https://github.com/mmontes11/charts/blob/main/deploy/charts/photoprism/templates/deployment.yml)). |
| `Service` | `v1` | `ClusterIP:80` ([service.yaml](https://github.com/mmontes11/charts/blob/main/deploy/charts/photoprism/templates/service.yaml)). |
| `ConfigMap` ×3 | `v1` | `<fullname>-environment` (the `env.*` block), `<fullname>-storage` (HTTP host/port, DB driver, originals/import/storage paths), and `<fullname>-vision` (only when `vision.enabled` without `configVolume`) ([configmap.yaml](https://github.com/mmontes11/charts/blob/main/deploy/charts/photoprism/templates/configmap.yaml)). |
| `CronJob` (one per job) | `batch/v1` | Only when `batch.enabled`; defaults `vision-labels` and `vision-caption`; `concurrencyPolicy: Forbid`, `backoffLimit: 5`, `restartPolicy: OnFailure`; same image/env/volumes as the Deployment; per-job `name`, `cron`, `suspend`, `command`, `args` ([cronjob.yml](https://github.com/mmontes11/charts/blob/main/deploy/charts/photoprism/templates/cronjob.yml)). |
| `IngressRoute` | Traefik CRD | Optional; host + entryPoints ([ingressRoute.yaml](https://github.com/mmontes11/charts/blob/main/deploy/charts/photoprism/templates/ingressRoute.yaml)). |
| `HTTPRoute` | Gateway API CRD | Optional; host + `gatewayRef` ([httpRoute.yaml](https://github.com/mmontes11/charts/blob/main/deploy/charts/photoprism/templates/httpRoute.yaml)). |
| `PodDisruptionBudget` | `policy/v1` | Optional; `minAvailable` default `1` ([poddisruptionbudget.yml](https://github.com/mmontes11/charts/blob/main/deploy/charts/photoprism/templates/poddisruptionbudget.yml)). |

Key values ([values.yaml](https://github.com/mmontes11/charts/blob/main/deploy/charts/photoprism/values.yaml)):

| Value | Default | Purpose |
| ----- | ------- | ------- |
| `image.repository` / `image.tag` / `image.pullPolicy` | `photoprism/photoprism` / `""` (tag falls back to appVersion `251130`) / `IfNotPresent` | Container image. |
| `env.*` | `PHOTOPRISM_AUTH: true`, `PHOTOPRISM_HTTP_COMPRESSION: "gzip"`, `PHOTOPRISM_LOG_LEVEL: info`, `PHOTOPRISM_READONLY: true`, `PHOTOPRISM_SITE_AUTHOR/CAPTION/DESCRIPTION` | Env block rendered into the `-environment` ConfigMap. |
| `secretRef.name` | `photoprism` | Existing Secret merged into the pod env via `envFrom`. |
| `database.driver` / `database.dsnSecretKeyRef` | `mysql` / `{name: photoprism-dsn, key: dsn}` | Database backend; DSN pulled from the existing Secret `photoprism-dsn` (key `dsn`) and exported as `PHOTOPRISM_DATABASE_DSN`. |
| `persistence.enabled` / `persistence.nas` | `true` / `my.nas.hostname` | Storage switch; default NFS server host. With `enabled: false`, `originals`/`import`/`storage` fall back to `emptyDir`. |
| `persistence.volumes[]` / `persistence.volumeMounts[]` | three raw NFS volumes: `originals` (readOnly) → `/photoprism/originals`, `import` → `/photoprism/import`, `storage` → `/photoprism/storage` | Storage is a **raw-volume pass-through**, not the PVC pattern. |
| `batch.enabled` | `false` | Master switch for the CronJobs. |
| `batch.jobs[]` | `vision-labels` and `vision-caption`: `cron: "*/5 * * * *"`, `command: [photoprism]`, `args: [vision, run, -m, labels\|caption, --count, 1, --force]`, `suspend: false` | AI batch passes over the library. |
| `batch.resources` / `nodeSelector` / `affinity` / `tolerations` / `runtimeClassName` / `priorityClassName` | `{}` / `{}` / `{}` / `[]` / `""` / `""` | Scheduling for the CronJob pods. |
| `vision.*` | `enabled: false`, `mountPath: /photoprism/storage/config/vision.yml`, `subPath: vision.yml`, `configVolume: {}`; `config`: two Models (`labels`, `caption`) with `Model: gemma3:latest`, `Engine: ollama`, `Service.Uri: http://ollama:11434/api/generate` | Vision config mounted from the generated `-vision` ConfigMap, or any existing volume via `configVolume`. |
| `service.type` / `service.port` / `service.annotations` | `ClusterIP` / `80` / `{}` | Front Service. |
| `ingressRoute.enabled` / `host` / `entryPoints` | `false` / `photoprism.org` / `[websecure]` | Traefik IngressRoute. |
| `httpRoute.enabled` / `host` / `gatewayRef` | `false` / `photoprism.org` / `{name: traefik, namespace: networking}` | Gateway API HTTPRoute. |
| `livenessProbe` / `readinessProbe` | `initialDelaySeconds: 20`, `periodSeconds: 30`, `failureThreshold: 5`, `timeoutSeconds: 5` | Probe tuning (probes themselves are `httpGet /` on port `http`). |
| `resources` / `nodeSelector` / `affinity` / `tolerations` / `runtimeClassName` / `priorityClassName` | `{}` / `{}` / `{}` / `[]` / `""` / `""` | Deployment scheduling (the chart sets no default resources). |
| `podDisruptionBudget.enabled` / `minAvailable` | `false` / `1` | Optional PDB. |

## Other charts

| Chart | Surface summary |
| ----- | --------------- |
| `bankroach` | Deploys a small Go CRUD app talking to CockroachDB ([templates/](https://github.com/mmontes11/charts/tree/main/deploy/charts/bankroach/templates), [values.yaml](https://github.com/mmontes11/charts/blob/main/deploy/charts/bankroach/values.yaml)). |
| `cockroachdb-operator` | Deploys the CockroachDB operator ([templates/](https://github.com/mmontes11/charts/tree/main/deploy/charts/cockroachdb-operator/templates)). |
| `echoperator` | Deploys a demo operator for an `echo` CRD ([templates/](https://github.com/mmontes11/charts/tree/main/deploy/charts/echoperator/templates)). |
| `github-explorer` | Deploys a React + Apollo GraphQL GitHub explorer UI ([templates/](https://github.com/mmontes11/charts/tree/main/deploy/charts/github-explorer/templates)). |
| `iot` | Deploys a general-purpose IoT platform ([templates/](https://github.com/mmontes11/charts/tree/main/deploy/charts/iot/templates)). |
| `mariadb` | Single-node MariaDB ([templates/](https://github.com/mmontes11/charts/tree/main/deploy/charts/mariadb/templates)). |
| `mongodb` | Single-node MongoDB, "with metrics compatible with ARM" ([templates/](https://github.com/mmontes11/charts/tree/main/deploy/charts/mongodb/templates), [release notes](https://github.com/mmontes11/charts/releases/tag/mongodb-0.5.0)). |
| `redis` | Single-node Redis, "with metrics compatible with ARM" ([templates/](https://github.com/mmontes11/charts/tree/main/deploy/charts/redis/templates), [release notes](https://github.com/mmontes11/charts/releases/tag/redis-0.4.0)). |

Per-chart version on `main` is the `Chart.yaml` field at the linked path; the latest *published* version per chart is listed in [README.md](README.md).
