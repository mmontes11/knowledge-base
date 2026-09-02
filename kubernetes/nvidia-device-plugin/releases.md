---
upstream: https://github.com/NVIDIA/k8s-device-plugin
last_updated: 2026-08-23
---

# nvidia-device-plugin — releases

Latest 10 official releases, newest first. Check the ⚠️ entries before upgrading. Upgrades are a rolling DaemonSet rollout: NVIDIA recommends draining GPU workloads first, since running GPU containers are not guaranteed to survive a rolling upgrade ([upgrading guide](https://github.com/NVIDIA/k8s-device-plugin#upgrading-kubernetes-with-the-device-plugin)). Full history in the [CHANGELOG](https://github.com/NVIDIA/k8s-device-plugin/blob/main/CHANGELOG.md).

## v0.20.0 — 2026-08-19

[Release page](https://github.com/NVIDIA/k8s-device-plugin/releases/tag/v0.20.0)

- ⚠️ **Configurable packed/distributed allocation policy** for replicated and MIG resources (#1621): allocation behaviour for shared resources is now a configuration choice; prefer distinct physical GPUs when breaking distributed-allocation ties (#1788). Review your sharing/allocation settings when upgrading.
- ⚠️ **MIG resource naming fix**: MIG profiles with suffixes such as `-me`, `+me.all`, and `+gfx` are now exposed as **separate** Kubernetes resources (#1807) — previously they could be collapsed; adjust Pod specs that request such profiles.
- Added support for the Rubin architecture family (#1909); NVML errors are surfaced when MIG device placement cannot be determined (#1899).
- Bumped the NVIDIA Container Toolkit to v1.20.0 (#1958), go-nvlib to v0.12.0 (#1911) and the Node Feature Discovery chart to v0.19.0 via OCI artifacts (#1922); added OCI standard image labels (#1961).

## v0.19.3 — 2026-06-22

[Release page](https://github.com/NVIDIA/k8s-device-plugin/releases/tag/v0.19.3)

- ⚠️ **Reverted the default enablement of `gds`, `gdrcopy` and `mofed` features** (#1837), which v0.19.0 had turned on by default — CDI gated modes are disabled again unless configured (consistent with v0.18.0's "gated modes optional" behaviour).
- Bumped the distroless Go base image to `v4.0.8-dev` (#1852) and Go to 1.26.4 (#1828).

## v0.19.2 — 2026-05-27

[Release page](https://github.com/NVIDIA/k8s-device-plugin/releases/tag/v0.19.2)

- ⚠️ **Dedicated service account in the device-plugin Helm chart** (#1804): the chart no longer shares the default ServiceAccount — GFD's node-label RBAC is granted to a purpose-built SA.
- GFD: added support for injecting `/dev/dri*` device nodes for gfx MIGs (#1785).
- Bumped the Container Toolkit go module to v1.19.1 (#1776) and go-nvlib/selinux/x-net/x-mod dependencies (#1774).

## v0.19.1 — 2026-04-23

[Release page](https://github.com/NVIDIA/k8s-device-plugin/releases/tag/v0.19.1)

- WSL: the plugin reports a single `"all"` device to kubelet on Windows Subsystem for Linux nodes (#1699).
- Fixed CDI spec generation to respect the driver root for Tegra CSV files (#1701).
- Dependency bumps only (golang 1.26.2, distroless `v4.0.4-dev`, grpc 1.79.3, k8sio group).

## v0.19.0 — 2026-03-17

[Release page](https://github.com/NVIDIA/k8s-device-plugin/releases/tag/v0.19.0)

- ⚠️ **Node Feature API enabled by default in GFD** (#1504): GFD now creates `NodeFeature` objects (with ownerReferences for garbage collection, #1597) in addition to node labels — requires the Node Feature Discovery API (bundled NFD chart is on v0.19.0). Disable if your setup does not serve that API.
- ⚠️ **Changed default values for `gds`, `gdrcopy` and `mofed` flags** (#1550) — this default was reverted in v0.19.3; see the entry above.
- Added `--sleep-interval=infinite` to GFD for running it as a Pod (#1603); fixed image tag in the static deployment (#1604); fixed health checking on old devices (#1562).

## v0.18.2 — 2026-01-23

[Release page](https://github.com/NVIDIA/k8s-device-plugin/releases/tag/v0.18.2)

- Ensured that `cdi.FeatureFlags` are passed to the CDI library.
- Fixed a race condition in the config-manager when the `nvidia.com/device-plugin.config` label is unset.
- Fixed nested container use cases by not mounting IPC sockets read-only; bumped the Container Toolkit to v1.18.2 and the distroless base to v3.2.2-dev.

## v0.18.1 — 2026-01-07

[Release page](https://github.com/NVIDIA/k8s-device-plugin/releases/tag/v0.18.1)

- Allowed CDI feature flags to be set (`cdi.featureFlags` / `--cdi-feature-flags`).
- Passed the driver root to `nvinfo.New` in the device-plugin main (correct device info for non-`/` driver roots, e.g. Tegra).
- Bumped the Container Toolkit to v1.18.1, the distroless base to v3.2.1-dev, and selinux to v1.13.1 (#1506).

## v0.18.0 — 2025-10-21

[Release page](https://github.com/NVIDIA/k8s-device-plugin/releases/tag/v0.18.0)

- ⚠️ **Gated CDI modes (GDS, MOFED, GDRCOPY) are now optional** and opt-in; added support for setting `gdrcopyEnabled` — previously gated content was generated unconditionally.
- ⚠️ **`nvidia.com/gpu.memory` label is now reported in MiB instead of MB** (per the v0.18.0 changelog "Use MiB instead of MB for gpu-memory"); adjust any scheduling logic or thresholds that assume MB.
- ⚠️ **Removed the `nvidia.com/gpu.imex-domain` label**; updated the README for RuntimeClass usage.
- Added Blackwell architecture detection; XID health checks can now be enabled explicitly (`getHealthCheckXids` renamed, XID 109 ignored); `mps-control-daemon` gets `hostPID` by default (#1045); switched logging to klog and to the distroless golang build image.

## v0.17.4 — 2025-09-09

[Release page](https://github.com/NVIDIA/k8s-device-plugin/releases/tag/v0.17.4)

- Ensured that directory volumes have `Directory` type (fixes CSI/mount edge cases).
- CUDA base image bumped to `13.0.0-base-ubi9` (#1369); go-nvlib bumped to 0.7.4 (#1346).
- Bug-fix/dependency release (NVML memory-read errors are now ignored rather than fatal, #1374).

## v0.17.3 — 2025-07-24

[Release page](https://github.com/NVIDIA/k8s-device-plugin/releases/tag/v0.17.3)

- Dependency bumps only: Container Toolkit 1.17.8, go-nvml 0.12.9-0, CUDA base `12.9.1-base-ubi9`, golang 1.23.11, oauth2 backport.
