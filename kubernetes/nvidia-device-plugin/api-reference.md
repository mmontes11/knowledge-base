---
upstream: https://github.com/NVIDIA/k8s-device-plugin
last_updated: 2026-08-23
---

# nvidia-device-plugin — API reference

The device plugin defines **no custom resource kinds of its own**. Its API surface consists of: (1) the standard Kubernetes objects the Helm chart and static manifests create, (2) the **extended resources** it registers with the kubelet, which users reference in Pod specs, and (3) the **node labels** it reads (as configuration) and writes (as information). This is a Helm chart / DaemonSet project, so the tables below list those surfaces; field-level behaviour is documented in the [README](https://github.com/NVIDIA/k8s-device-plugin#readme) and linked sections.

## Kubernetes objects shipped (Helm chart / static manifests)

| Object | Kind | Description | Upstream docs |
| ------ | ---- | ----------- | ------------- |
| `nvidia-device-plugin` | `DaemonSet` | The plugin itself, one Pod per GPU node. Registers the NVIDIA devices with the kubelet and advertises the extended resources below. Runs `nvcr.io/nvidia/k8s-device-plugin`; node affinity matches `feature.node.kubernetes.io/pci-10de.present`, NVIDIA Tegra CPUs, or the forced `nvidia.com/gpu.present` label (see [values.yaml](https://github.com/NVIDIA/k8s-device-plugin/blob/main/deployments/helm/nvidia-device-plugin/values.yaml)). | [deployment via `helm`](https://github.com/NVIDIA/k8s-device-plugin#deployment-via-helm) |
| `gpu-feature-discovery` | `DaemonSet` | GPU Feature Discovery (GFD, since v0.15.0): writes GPU/driver/CUDA labels to the node via Node Feature Discovery. Can also run as a one-shot Job. | [GFD docs](https://github.com/NVIDIA/k8s-device-plugin/blob/main/docs/gpu-feature-discovery/README.md#deploy-nvidia-gpu-feature-discovery-gfd) |
| `mps-control-daemon` | `DaemonSet` | Optional control daemon for the experimental CUDA MPS sharing path; manages per-client memory/compute partitions. `hostPID` defaults to true. | [MPS section in README](https://github.com/NVIDIA/k8s-device-plugin#with-cuda-mps) |
| `<name>-configs` or external `ConfigMap` | `ConfigMap` | One or more named plugin configuration files (YAML, `version: v1`): `flags` (`migStrategy`, `failOnInitError`, `nvidiaDriverRoot`, `gdrcopyEnabled`, `gdsEnabled`, `mofedEnabled`), the `plugin` section (`passDeviceSpecs`, `deviceListStrategy`, `deviceIDStrategy`), and `sharing` (`timeSlicing`/`mps` with `replicas`, `renameByDefault`, `failRequestsGreaterThanOne`). Use `config.name` to reference an external ConfigMap or `config.map` to build an integrated one. | [passing configuration via a ConfigMap](https://github.com/NVIDIA/k8s-device-plugin#passing-configuration-to-the-plugin-via-a-configmap) |
| ServiceAccount / Role / RoleBinding | RBAC | Permissions for GFD to read/patch node labels; a dedicated service account in the chart since v0.19.2. | [chart templates](https://github.com/NVIDIA/k8s-device-plugin/tree/main/deployments/helm/nvidia-device-plugin/templates) |
| `NodeFeature` (NFD API, optional) | CRD (upstream: `node.k8s.io`) | Since v0.19.0 GFD uses the Node Feature API by default, creating `NodeFeature` objects (with ownerReferences for garbage collection) instead of/in addition to plain node labels. | [GFD docs](https://github.com/NVIDIA/k8s-device-plugin/blob/main/docs/gpu-feature-discovery/README.md), [v0.19.0 release](https://github.com/NVIDIA/k8s-device-plugin/releases/tag/v0.19.0) |

## Extended resources (what you request in a Pod spec)

Registered with the kubelet by the plugin; referenced via `resources.limits`:

| Resource | Description | Upstream docs |
| -------- | ----------- | ------------- |
| `nvidia.com/gpu` | Whole-GPU replicas. If a Pod does not request GPUs but runs with this plugin, all node GPUs are exposed to the container (see [warning](https://github.com/NVIDIA/k8s-device-plugin#running-gpu-jobs)). | [Running GPU Jobs](https://github.com/NVIDIA/k8s-device-plugin#running-gpu-jobs) |
| `<resource>.shared` (typically `nvidia.com/gpu.shared`) | Oversubscribed replica of a resource when time-slicing or MPS sharing is configured with `renameByDefault: true`; replicas multiply the advertised count (e.g. 8 GPUs × 10 replicas = 80). | [Shared access to GPUs](https://github.com/NVIDIA/k8s-device-plugin#shared-access-to-gpus) |
| `nvidia.com/mig-<slice_count>g.<memory_size>` (e.g. `nvidia.com/mig-1g.5gb`) | Individual MIG slices when `MIG_STRATEGY=mixed`. Since v0.20.0, profiles with suffixes (`-me`, `+me.all`, `+gfx`, …) are exposed as separate resources. | [MIG section in README](https://github.com/NVIDIA/k8s-device-plugin#configuration-option-details), [v0.20.0 release](https://github.com/NVIDIA/k8s-device-plugin/releases/tag/v0.20.0) |

The device list itself is passed to the container runtime via the `DEVICE_LIST_STRATEGY`: `envvar` (default, `NVIDIA_VISIBLE_DEVICES`), `volume-mounts`, `cdi-annotations`, or `cdi-cri`; device identity via `DEVICE_ID_STRATEGY` `uuid` (default) or `index`. See the [README configuration reference](https://github.com/NVIDIA/k8s-device-plugin#configuration-option-details) and [CDI notes](https://github.com/NVIDIA/k8s-device-plugin/blob/main/docs/cdi.md).

## Node labels — configuration (read by the plugin)

From the [Catalog of Labels](https://github.com/NVIDIA/k8s-device-plugin#catalog-of-labels):

| Label | Purpose |
| ----- | ------- |
| `nvidia.com/device-plugin.config` | Selects which named configuration file in the ConfigMap applies to this node (per-node configuration; see [Updating Per-Node Configuration With a Node Label](https://github.com/NVIDIA/k8s-device-plugin#updating-per-node-configuration-with-a-node-label)). |
| `nvidia.com/gpu.sharing-strategy` | `none` (default), `mps`, or `time-slicing`. |
| `nvidia.com/mig.capable` | Whether any device on the node supports MIG. |
| `nvidia.com/mps.capable` | Whether devices are configured for MPS. |
| `nvidia.com/vgpu.present` | Whether the node's devices use vGPU. |
| `nvidia.com/vgpu.host-driver-branch` / `nvidia.com/vgpu.host-driver-version` | vGPU host driver branch and version on the hypervisor. |
| `nvidia.com/gpu.present` | Force label: makes the chart's default affinity place the DaemonSet on the node (also the usual "has GPUs" marker). |

Option precedence for the plugin flags is (1) command-line flag, (2) environment variable, (3) configuration file — [README](https://github.com/NVIDIA/k8s-device-plugin#as-command-line-flags-or-envvars).

## Node labels — informational (written by GFD / plugin)

Generated by GPU Feature Discovery ([GFD labels table](https://github.com/NVIDIA/k8s-device-plugin/blob/main/docs/gpu-feature-discovery/README.md#generated-labels)): `nvidia.com/gpu.count`, `nvidia.com/gpu.product`, `nvidia.com/gpu.memory` (in **MiB**, not MB — see the v0.18.0 changelog), `nvidia.com/gpu.compute.major`/`.minor`, `nvidia.com/gpu.family`, `nvidia.com/gpu.machine`, `nvidia.com/gpu.replicas`, `nvidia.com/gpu.mode`, `nvidia.com/gpu.clique`, `nvidia.com/cuda.driver-version.{major,minor,revision,full}`, `nvidia.com/cuda.runtime-version.{major,minor,full}`, `nvidia.com/gfd.timestamp`, plus MIG-strategy-specific labels (`nvidia.com/mig.strategy`, `nvidia.com/gpu.slices.gi`/`.ci`, `nvidia.com/gpu.engines.*`, per-type `nvidia.com/mig-<type>.*` under `mixed`).

- The older label families `nvidia.com/cuda.driver.{major,minor,rev}` and `nvidia.com/cuda.runtime.{major,minor}` were **deprecated** in v0.15.0, in favor of the `-version` forms.
- `nvidia.com/gpu.imex-domain` was **removed** in v0.18.0.
- `nvidia.com/gpu.product` may be modified by the device plugin when a sharing strategy is active; `nvidia.com/gpu.count` is overridden under MIG `single` to count MIG devices.

## Helm values surface (selection)

The full value list is [values.yaml](https://github.com/NVIDIA/k8s-device-plugin/blob/main/deployments/helm/nvidia-device-plugin/values.yaml). The main levers:

- `image.repository`/`image.tag` (default `nvcr.io/nvidia/k8s-device-plugin`), `updateStrategy`, `namespaceOverride`, `allowDefaultNamespace`
- Direct flag overrides (optional, override the ConfigMap): `migStrategy`, `failOnInitError`, `deviceListStrategy`, `deviceIDStrategy`, `nvidiaDriverRoot`, `gdrcopyEnabled`, `gdsEnabled`, `mofedEnabled`, `deviceDiscoveryStrategy`, `compatWithCPUManager`
- `config.name` / `config.map` / `config.default` / `config.fallbackStrategies` — ConfigMap-based configuration (preferred since v0.12.0)
- `devicePlugin.enabled`, `gfd.enabled` (+ `gfd.sleepInterval`, `gfd.noTimestamp`), `nfd.*` (Node Feature Discovery subchart), `mps.root` / `mps.enableHostPID` / `mps.enableHostNetwork`
- `cdi.nvidiaHookPath`, `cdi.featureFlags` — CDI spec generation
- `affinity`, `tolerations` (defaults tolerate the `nvidia.com/gpu` NoSchedule taint), `priorityClassName` (default `system-node-critical`), `runtimeClassName`, `resources`, `nodeSelector`

Notes:

- The plugin registers on the kubelet's Unix socket; there is no network API.
- The GFD binary is considered beta (may break before its own `v1.0.0`) — [GFD docs](https://github.com/NVIDIA/k8s-device-plugin/blob/main/docs/gpu-feature-discovery/README.md#beta-version).
- Field-level behaviour is intentionally not duplicated here; follow the per-surface upstream links above.
