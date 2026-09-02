---
upstream: https://github.com/NVIDIA/dcgm-exporter
last_updated: 2026-08-23
---

# nvidia-dcgm-exporter — features

Feature areas of DCGM Exporter, each linked to the upstream documentation. Official docs: [docs.nvidia.com — DCGM Exporter](https://docs.nvidia.com/datacenter/cloud-native/gpu-telemetry/dcgm-exporter.html); the [repository README](https://github.com/NVIDIA/dcgm-exporter) and [llms.txt metrics/deployment contract](https://github.com/NVIDIA/dcgm-exporter/blob/main/llms.txt) are the canonical references for configuration.

## Metric collection

- **DCGM field watch list** — the exporter watches any set of DCGM fields (device health, clocks, temperature, power, ECC, retired pages, NVLink, XID). The default list ships as `/etc/dcgm-exporter/default-counters.csv`; the watch list is a complete (not additive) `DCGM FIELD, metric type, help` CSV supplied via `-f`/`--collectors`, `-m <namespace>:<configmap>`, or `DCGM_EXPORTER_COLLECT`. [README: Changing Metrics](https://github.com/NVIDIA/dcgm-exporter#changing-metrics)
- **Customizable collection** — per-watch polling intervals, combined device selectors, and a YAML configuration file (metric file path + collection interval) added in `4.6.0-4.8.3`; device selection with `f`/`g[:range]`/`i[:range]`. [release notes](https://github.com/NVIDIA/dcgm-exporter/releases/tag/4.6.0-4.8.3)
- **Profiling (DCP) metrics** — hardware performance counters `DCGM_FI_PROF_*` (GPU engine, SM, pipeline, DRAM, PCIe activity) require the `SYS_ADMIN` capability (and DCGM's profiling package for Ada and older GPUs). [values: `securityContext`](https://github.com/NVIDIA/dcgm-exporter/blob/main/deployment/values.yaml), [release 4.1.1-4.0.4](https://github.com/NVIDIA/dcgm-exporter/releases/tag/4.1.1-4.0.4)

## Prometheus exposition

- **HTTP endpoints** — `:9400/metrics` (Prometheus text format), `/health` status probe, and opt-in `/debug/pprof/` (since `4.5.3-4.8.2`, protected by web config). [README](https://github.com/NVIDIA/dcgm-exporter#pprof-profiling-endpoints)
- **Standard labels** — `gpu`, `UUID`, and `hostname` (⚠️ renamed from `Hostname` in `4.6.0-4.8.3`) on every device series, plus `dcgm_exporter_build_metadata`. [release notes](https://github.com/NVIDIA/dcgm-exporter/releases/tag/4.6.0-4.8.3)
- **Secure scraping** — TLS and basic authentication via [exporter-toolkit](https://github.com/prometheus/exporter-toolkit) `--web-config-file`; Helm `service` web read/write timeouts and `internalTrafficPolicy` are configurable. [README: TLS and Basic Auth](https://github.com/NVIDIA/dcgm-exporter#tls-and-basic-auth)
- **ServiceMonitor** — Prometheus Operator support with relabeling (this repository relabels `job`→`instance` so each node/GPU pair appears as its own Prometheus instance). [service-monitor.yaml](https://github.com/NVIDIA/dcgm-exporter/blob/main/service-monitor.yaml)

## Kubernetes integration

- **Pod label and UID enrichment** — with `DCGM_EXPORTER_KUBERNETES` (chart `kubernetes.enablePodLabels` / `enablePodUID`) and the chart-provisioned ClusterRole/Binding, each GPU series is labeled with the pod(s) using that GPU (labels, UID); pod label filtering allow-list since `4.4.1-4.6.0`. Disabled in this repository (post-render patch sets it to `false`) to export one series set per GPU.
- **MIG awareness** — metrics per GPU and per MIG instance, pod-label collection on MIG devices (since `4.6.0-4.8.3`), HPC job labels for MIG (since `4.5.3-4.8.2`). [release notes](https://github.com/NVIDIA/dcgm-exporter/releases/tag/4.6.0-4.8.3)
- **DRA (Dynamic Resource Allocation)** — tracks GPUs allocated via [k8s-dra-driver](https://github.com/NVIDIA/k8s-dra-driver-gpu) (`kubernetesDRA.enabled`, ResourceSlice v1 since `4.6.0-4.8.3`), including unallocated GPUs (since `4.4.2-4.7.0`).
- **GPU bind/unbind events** — (beta since `4.5.1-4.8.0`) the exporter monitors GPU allocation changes and reloads automatically. [release notes](https://github.com/NVIDIA/dcgm-exporter/releases/tag/4.5.1-4.8.0)

## Health, errors, and throttling

- **XID and error counters** — `DCGM_FI_DEV_XID_ERRORS` plus cumulative XID event totals, clock-event totals, and GPU-health severity/category metadata (since `4.6.0-4.8.3`); XID texts track driver "XID errors v580" documentation (since `4.4.2-4.7.1`).
- **Health watch** — `--health-watch` / `DCGM_EXPORTER_HEALTH_WATCH` enables DCGM health checks (XID, row-remap, fallback, hang, etc.). [README/CLI docs](https://github.com/NVIDIA/dcgm-exporter)
- **ECC, row remap, retired pages** — volatile/aggregate ECC totals, remapped rows, remap failure, and retired-page counters for hardware degradation tracking. [default counter list](https://github.com/NVIDIA/dcgm-exporter/blob/main/etc/default-counters.csv)
- **Throttling violations** — power/thermal/sync-boost/board-limit/low-util/reliability violation counters (throttling duration) for capacity diagnosis.

## NVLink and PCIe

- **NVLink health** — CRC flit/data error counters, replay/recovery counters, and per-lane bandwidth counters (bandwidth metrics exposed as gauges since `4.6.0-4.8.3`).
- **P2P status** — `DCGM_EXP_P2P_STATUS` (since `4.3.1-4.4.0`, mapping fix in `4.5.1-4.8.0`) reports peer-to-peer NVLink status matrix for multi-GPU topologies.
- **PCIe throughput** — TX/RX throughput counters and PCIe replay counters. [default counter list](https://github.com/NVIDIA/dcgm-exporter/blob/main/etc/default-counters.csv)

## Workload and environment mapping

- **HPC job mapping** — `--hpc-job-mapping-dir` / `DCGM_HPC_JOB_MAPPING_DIR` maps each GPU/MIG instance to the HPC job IDs running on it, added as metric labels. [README: HPC jobs](https://github.com/NVIDIA/dcgm-exporter#how-to-include-hpc-jobs-in-metric-labels)
- **Per-process GPU metrics** — per-process utilization for time-shared and MIG devices (since `4.5.3-4.8.2`), with independent label maps per entity (since `4.6.0-4.8.3`).
- **Container runtime labels** — `--container-labels` + `--container-runtime-socket` (since `4.6.0-4.8.3`) add the host container name (or short ID) to explicitly assigned GPUs. [README: Runtime container labels](https://github.com/NVIDIA/dcgm-exporter#runtime-container-labels)
- **Grace CPU metadata** — `cpu_serial` label on per-CPU metrics when DCGM reports it (since `4.6.0-4.8.3`).

## Deployment

- **Docker** — single image `nvcr.io/nvidia/k8s/dcgm-exporter`; quickstart is `docker run -d --gpus all --cap-add SYS_ADMIN -p 9400:9400 ...`. [README](https://github.com/NVIDIA/dcgm-exporter#quickstart)
- **Helm chart** — `dcgm-exporter` from `https://nvidia.github.io/dcgm-exporter/helm-charts`; distroless image since `4.5.1-4.8.0`; image tag/digest pinning, `hostPID`, priority class, TLS, and ServiceMonitor all values. In this repository it is applied via Flux `HelmRelease` `nvidia-dcgm-exporter` in namespace `gpu`.
- **GPU Operator** — on Kubernetes the exporter is normally shipped by the [NVIDIA GPU Operator](https://github.com/NVIDIA/gpu-operator) as its `dcgm-exporter` component; deploying the chart directly is recommended only when the Operator is not used. [README](https://github.com/NVIDIA/dcgm-exporter#quickstart-on-kubernetes)
- **Host systemd** — the RPM/DEB package ships `nvidia-dcgm-exporter.service` with `Restart=on-failure`, 10s restart delay, and no start-rate limit, so transient DCGM/driver disruption does not leave the exporter stopped. [README: Host systemd deployments](https://github.com/NVIDIA/dcgm-exporter#host-systemd-deployments)
- **Remote hostengine** — collect from nodes running `nv-hostengine` elsewhere via `tcp://`, `unix:///<socket>`, or `vsock://<CID>:<PORT>` (VSOCK since `4.6.0-4.8.3`); IPv6 requires bracket notation. [README: Remote Hostengine URI Formats](https://github.com/NVIDIA/dcgm-exporter#remote-hostengine-uri-formats)

## Visualization

- **Grafana dashboards** — ready-made GPU dashboards ship in the [`grafana/` directory](https://github.com/NVIDIA/dcgm-exporter/tree/main/grafana); OpenObserve dashboards are referenced in the README since `4.4.2-4.7.1`.
- **Alerting** — no Prometheus rules ship with the chart; `mmontes11/k8s-infrastructure` adds its own `PrometheusRule` (`HighGpuUsage` > 85% over 5m, `HighGpuTemperature` > 85°C over 5m) in `infrastructure/nvidia-dcgm-exporter/alerts/`.
