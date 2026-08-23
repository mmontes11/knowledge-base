---
upstream: https://github.com/NVIDIA/dcgm-exporter
last_updated: 2026-08-23
---

# nvidia-dcgm-exporter — API reference

DCGM Exporter is not a CRD project: the `dcgm-exporter` Helm chart (deployed at version `4.8.2` in this repository, from chart repo `https://nvidia.github.io/dcgm-exporter/helm-charts`) installs a small set of standard Kubernetes objects and exposes its "API" over an HTTP metrics endpoint. Chart values reference: [deployment/values.yaml](https://github.com/NVIDIA/dcgm-exporter/blob/main/deployment/values.yaml).

## Kubernetes objects created by the chart

| Kind | Name (default) | Scope | Purpose | Upstream docs |
| --- | --- | --- | --- | --- |
| `DaemonSet` | `nvidia-dcgm-exporter` | Namespaced | One exporter Pod per node; runs the `dcgm-exporter` binary against the local DCGM hostengine via the node's NVIDIA device plugins (`runtimeClassName`, `hostPID`, `SYS_ADMIN` capability for profiling fields). | [Kubernetes deployment (README)](https://github.com/NVIDIA/dcgm-exporter#quickstart-on-kubernetes) |
| `Service` | `nvidia-dcgm-exporter` | Namespaced | ClusterIP service fronting the exporter (`:9400` by default); `internalTrafficPolicy` and web read/write timeouts are values. | [values: `service`](https://github.com/NVIDIA/dcgm-exporter/blob/main/deployment/values.yaml) |
| `ConfigMap` | `<release>-dcgm-exporter-config` | Namespaced | YAML exporter configuration (metric file, collection interval); enabled by `config.enabled`/`config.create`, or point `config.name` at an existing ConfigMap. | [values: `config`](https://github.com/NVIDIA/dcgm-exporter/blob/main/deployment/values.yaml) |
| `ServiceAccount` + `ClusterRole`/`ClusterRoleBinding` | `<release>`-derived | Cluster | Grants the read access to pods needed for pod label/UID enrichment and DRA; created only when `kubernetes.enablePodLabels`, `kubernetes.enablePodUID`, or `kubernetesDRA.enabled` are true (or SA token mounting is). | [values: `kubernetes.rbac`](https://github.com/NVIDIA/dcgm-exporter/blob/main/deployment/values.yaml) |
| `ServiceMonitor` | `<release>-dcgm-exporter` (monitoring.coreos.com/v1) | Namespaced | Prometheus Operator scrape config for `:9400/metrics`; enabled by `serviceMonitor.enabled`, with `interval`, `scrapeTimeout`, `relabelings`, `metricRelabelings`. | [service-monitor.yaml](https://github.com/NVIDIA/dcgm-exporter/blob/main/service-monitor.yaml) |

## HTTP surface (per Pod, port 9400)

| Endpoint | Purpose | Upstream docs |
| --- | --- | --- |
| `GET /metrics` | Prometheus exposition of all watched DCGM fields (`DCGM_FI_*`, `DCGM_EXP_*`, `DCGM_CUPTI_*` names) with per-GPU labels. | [README quickstart example](https://github.com/NVIDIA/dcgm-exporter#quickstart) |
| `GET /health` | Health/status probe (behavior fixed in release `4.5.1-4.8.0`). | [release notes](https://github.com/NVIDIA/dcgm-exporter/releases/tag/4.5.1-4.8.0) |
| `GET /debug/pprof/` | Go profiling endpoints; opt-in via `--enable-pprof` / `DCGM_EXPORTER_ENABLE_PPROF`, requires `--web-config-file` so it is protected by TLS/basic auth. | [README: Pprof profiling endpoints](https://github.com/NVIDIA/dcgm-exporter#pprof-profiling-endpoints) |

## Metric label surface

Baseline labels on every device series: `gpu` (index), `UUID` (GPU UUID), `hostname` (⚠️ renamed from `Hostname` in release `4.6.0-4.8.3`), plus `dcgm_exporter_build_metadata`. Optional enrichment labels: `kubernetes_pod`, `kubernetes_namespace`, `kubernetes_container` (pod mapping), `container` (host container runtime, added in `4.6.0-4.8.3`), `cpu_serial` (Grace CPU serials, `4.6.0-4.8.3`). See [README](https://github.com/NVIDIA/dcgm-exporter) and [llms.txt metrics contract](https://github.com/NVIDIA/dcgm-exporter/blob/main/llms.txt).

## Configuration surface (CLI flags / environment)

The chart passes through `arguments: []` and `extraEnv`; the most relevant switches (see [README](https://github.com/NVIDIA/dcgm-exporter) for details):

| Setting | Purpose |
| --- | --- |
| `-f` / `--collectors` (CSV) or `-m <namespace>:<configmap>` | Custom/complete metric watch list in `DCGM FIELD, metric type, help` format (default: `/etc/dcgm-exporter/default-counters.csv`). |
| `DCGM_EXPORTER_COLLECT` | Environment equivalent of the field watch list. |
| `-d <devices>` | Device selector: `f` (all, default), `g[:range]`, `i[:range]` (MIG), or combined. |
| `-r` / `DCGM_REMOTE_HOSTENGINE_INFO` | Remote hostengine: `HOST:PORT` or `tcp://`, `unix:///<socket>`, `vsock://<CID>:<PORT>` (added in `4.6.0-4.8.3`), IPv6 requires bracket notation. |
| `-a` / `DCGM_EXPORTER_LISTEN` | Metrics listen address (default `:9400`). |
| `DCGM_EXPORTER_INTERVAL` | Watch polling interval. |
| `DCGM_EXPORTER_KUBERNETES` | Enable pod-label enrichment mode on Kubernetes (requires RBAC). |
| `DCGM_EXPORTER_CUSTOM_METADATA` | Extra labels to attach to every metric. |
| `DCGM_EXPORTER_P2P_STATUS` | Enable P2P/NVLink peer-to-peer status metrics (`DCGM_EXP_P2P_STATUS`). |
| `--hpc-job-mapping-dir` / `DCGM_HPC_JOB_MAPPING_DIR` | Map GPUs to HPC job IDs from job-mapping files. |
| `--web-config-file` | exporter-toolkit TLS/basic auth via [exporter-toolkit](https://github.com/prometheus/exporter-toolkit) web configuration. |
| YAML config file (chart `config.*`) | Startup configuration: metric file path and collection interval (added in `4.6.0-4.8.3`). |

In `mmontes11/k8s-infrastructure`, `infrastructure/nvidia-dcgm-exporter/nvidia-dcgm-exporter-helmrelease.yaml` pins chart version `4.8.2`, enables the ServiceMonitor with a `job`→`instance` relabeling, sets `runtimeClassName: nvidia`, node affinity on `nvidia.com/gpu.present`, tolerates `nvidia.com/gpu`/`node.mmontes.io/type=compute-xlarge`, and post-render patches the DaemonSet to set `DCGM_EXPORTER_KUBERNETES=false` so a single metric set per GPU is exported instead of per-pod series. `alerts/gpu-rules.yaml` defines the `HighGpuUsage` and `HighGpuTemperature` `PrometheusRule`s.
