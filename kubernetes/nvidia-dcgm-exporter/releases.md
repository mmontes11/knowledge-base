---
upstream: https://github.com/NVIDIA/dcgm-exporter
last_updated: 2026-10-01
---

# nvidia-dcgm-exporter — releases

Latest 10 official releases of `NVIDIA/dcgm-exporter`, newest first. Tags follow `<DCGM version>-<exporter version>` (release `4.8.4` dropped the DCGM prefix); scan the ⚠️ entries before upgrading. The version deployed in this repository (Helm chart pin) is `4.8.2` (release `4.5.3-4.8.2`).

## 4.8.4 — 2026-09-18
[Release page](https://github.com/NVIDIA/dcgm-exporter/releases/tag/4.8.4)
- DCGM 4.6.1 + go-dcgm v1.4611.2 (Go 1.27.1); YAML schema v2 with plural `collections` (v1-compatible), configurable DCGM field-watch retention limits, limits and request coalescing for concurrent `/metrics` scrapes, and support for metric configurations with more than 128 DCGM fields.
- ⚠️ Retired PCIe throughput fields removed from the supplied metric configurations — custom configurations using them now fail; use `DCGM_FI_PROF_PCIE_TX_BYTES` / `DCGM_FI_PROF_PCIE_RX_BYTES` instead.
- Optional GPU bind/unbind detection that resets DCGM/NVML and rebuilds metrics for the new topology ([#716](https://github.com/NVIDIA/dcgm-exporter/pull/716)); opt-in exporter self-metrics via `--enable-exporter-metrics`; opt-in GPU fabric-health and power-limit fields; MIG compute-instance-scoped fields with a `GPU_CI_ID` label; optional `pod_uid` label on DRA-attributed metrics ([#738](https://github.com/NVIDIA/dcgm-exporter/pull/738)).
- Helm: DaemonSet annotation and probe timing/failure-threshold values; dynamic linker cache fix for the distroless image ([#724](https://github.com/NVIDIA/dcgm-exporter/pull/724)); DRA MIG pod attribution refreshed when ResourceSlices change ([#714](https://github.com/NVIDIA/dcgm-exporter/issues/714)).

## 4.6.0-4.8.3 — 2026-07-15
[Release page](https://github.com/NVIDIA/dcgm-exporter/releases/tag/4.6.0-4.8.3)
- DCGM 4.6.0 + go-dcgm v1.4601.1; new YAML configuration-file support, configurable web read/write timeouts, per-watch polling intervals, and VSOCK remote hostengine connections.
- ⚠️ **Renamed the `Hostname` metric label to `hostname`** ([#655](https://github.com/NVIDIA/dcgm-exporter/pull/655)) — dashboards and alert rules referencing the old `Hostname` label must be updated.
- ⚠️ NVLink bandwidth metrics corrected from counters to gauges ([#658](https://github.com/NVIDIA/dcgm-exporter/pull/658)).
- DRA ResourceSlice v1 support and expanded DRA/MIG allocation handling; pod-label collection on MIG devices; independent label maps per entity; bounded pod-resources responses; cumulative XID/clock-event totals and GPU-health severity/category metadata.

## 4.5.3-4.8.2 — 2026-05-07
[Release page](https://github.com/NVIDIA/dcgm-exporter/releases/tag/4.5.3-4.8.2)
- DCGM 4.5.3 (the exporter/chart version pinned in this repository).
- Per-process GPU metrics for time-sharing and MIG ([#594](https://github.com/NVIDIA/dcgm-exporter/pull/594)); GPU-wide health incidents such as fallen-off-bus XIDs.
- `/debug/pprof` profiling endpoints made opt-in via `--enable-pprof` / `DCGM_EXPORTER_ENABLE_PPROF`; Helm `priorityClassName` configurable; PodMapper informer caching ([#626](https://github.com/NVIDIA/dcgm-exporter/pull/626)).

## 4.5.2-4.8.1 — 2026-02-09
[Release page](https://github.com/NVIDIA/dcgm-exporter/releases/tag/4.5.2-4.8.1)
- DCGM 4.5.2, Go 1.24, refreshed base containers.
- Fixed distroless symlink issue, blank-XID parsing, and NVLink entities starting at offset 1.

## 4.5.1-4.8.0 — 2026-01-28
[Release page](https://github.com/NVIDIA/dcgm-exporter/releases/tag/4.5.1-4.8.0)
- ⚠️ **Helm chart now uses a distroless container by default.**
- GPU bind/unbind event monitoring with automatic reload (beta); synced default metric watchlist between docker and helm.
- Default memory limit raised to 512Mi; `scrapeTimeout` configurable; fixed P2P status mappings and health-endpoint behavior.

## 4.4.2-4.7.1 — 2025-12-10
[Release page](https://github.com/NVIDIA/dcgm-exporter/releases/tag/4.4.2-4.7.1)
- Updated go-dcgm; XID error texts updated per "XID errors v580" ([#588](https://github.com/NVIDIA/dcgm-exporter/pull/588)).
- Fixed validation time unit (us→ns); README now references OpenObserve blog and dashboards.

## 4.4.2-4.7.0 — 2025-11-18
[Release page](https://github.com/NVIDIA/dcgm-exporter/releases/tag/4.4.2-4.7.0)
- Security hardening in startup and pipeline.
- ⚠️ Crashes if the collector cannot be initialized under startup validation ([#578](https://github.com/NVIDIA/dcgm-exporter/pull/578)) — misconfigured nodes now fail fast.
- Tracks unallocated GPUs in DRA; fixed unbounded label cache size ([#574](https://github.com/NVIDIA/dcgm-exporter/pull/574)).

## 4.4.1-4.6.0 — 2025-10-13
[Release page](https://github.com/NVIDIA/dcgm-exporter/releases/tag/4.4.1-4.6.0)
- Allow list for pod label filtering (`podLabelAllowlistRegex`); `hostPID` values/DaemonSet support ([#503](https://github.com/NVIDIA/dcgm-exporter/pull/503)).
- Option to fail on NVML provider init error ([#557](https://github.com/NVIDIA/dcgm-exporter/pull/557)); improved NVLink monitoring.

## 4.4.1-4.5.2 — 2025-09-17
[Release page](https://github.com/NVIDIA/dcgm-exporter/releases/tag/4.4.1-4.5.2)
- FHS-convention logging ([#556](https://github.com/NVIDIA/dcgm-exporter/pull/556)).
- Fixed enabling metrics without pods via `kubernetes-enable-dra` / `kubernetes-enable-virtual-gpus` ([#554](https://github.com/NVIDIA/dcgm-exporter/pull/554)); added `--disable-startup-validate` flag.

## 4.4.0-4.5.0 — 2025-08-19
[Release page](https://github.com/NVIDIA/dcgm-exporter/releases/tag/4.4.0-4.5.0)
- DCGM 4.4 and CUDA 13.0.
- Kubernetes pod-UID support; created the distroless container target.
