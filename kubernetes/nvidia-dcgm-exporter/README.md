---
upstream: https://github.com/NVIDIA/dcgm-exporter
last_updated: 2026-08-23
---

# nvidia-dcgm-exporter

[DCGM Exporter](https://docs.nvidia.com/datacenter/cloud-native/gpu-telemetry/dcgm-exporter.html) is a Prometheus exporter for NVIDIA GPU metrics built on top of [NVIDIA DCGM](https://developer.nvidia.com/dcgm). It runs one collector per GPU node (typically a DaemonSet), watches the DCGM field group (device health, clocks, temperature, power, ECC, XID errors, NVLink, profiling) and exposes the values in Prometheus text format on port 9400. In Kubernetes it can optionally enrich each series with the pod labels/UIDs of the workloads using the GPU, and it understands MIG devices and DRA (Dynamic Resource Allocation) GPUs.

- Upstream repository: [NVIDIA/dcgm-exporter](https://github.com/NVIDIA/dcgm-exporter)
- Official documentation: [https://docs.nvidia.com/datacenter/cloud-native/gpu-telemetry/dcgm-exporter.html](https://docs.nvidia.com/datacenter/cloud-native/gpu-telemetry/dcgm-exporter.html)
- Helm chart: `dcgm-exporter`, published at [https://nvidia.github.io/dcgm-exporter/helm-charts](https://nvidia.github.io/dcgm-exporter/helm-charts)
- Container image: `nvcr.io/nvidia/k8s/dcgm-exporter`
- License: Apache-2.0
- Release tags use the `<DCGM version>-<exporter version>` scheme (e.g. `4.6.0-4.8.3`)

This entry documents the exporter and chart as deployed by `mmontes11/k8s-infrastructure` in `infrastructure/nvidia-dcgm-exporter/` (Flux `HelmRelease` `nvidia-dcgm-exporter`, chart `dcgm-exporter` version `4.8.2`, namespace `gpu`, with a `PrometheusRule` for utilization/temperature alerts). Note: for clusters that already run the [NVIDIA GPU Operator](https://github.com/NVIDIA/gpu-operator), the exporter is normally deployed by the Operator rather than directly.

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
