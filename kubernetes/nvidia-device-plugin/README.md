---
upstream: https://github.com/NVIDIA/k8s-device-plugin
last_updated: 2026-08-23
---

# nvidia-device-plugin

[NVIDIA/k8s-device-plugin](https://github.com/NVIDIA/k8s-device-plugin) is NVIDIA's official implementation of the [Kubernetes device plugin](https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/device-plugins/). Deployed as a DaemonSet on GPU nodes, it exposes NVIDIA GPUs to the Kubernetes scheduler (as `nvidia.com/gpu` and related extended resources), tracks GPU health, and makes allocated devices available inside containers — and since v0.15.0 the same repository also ships the GPU Feature Discovery (GFD) implementation that labels nodes with driver/CUDA/GPU attributes through Node Feature Discovery.

- Upstream repository: https://github.com/NVIDIA/k8s-device-plugin
- Documentation: the repository [README](https://github.com/NVIDIA/k8s-device-plugin#readme) (canonical), [GPU Feature Discovery docs](https://github.com/NVIDIA/k8s-device-plugin/blob/main/docs/gpu-feature-discovery/README.md), [CDI notes](https://github.com/NVIDIA/k8s-device-plugin/blob/main/docs/cdi.md); runtime configuration details in the [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html)
- License: Apache-2.0
- Deployment: Helm chart `nvidia-device-plugin` from the `nvdp` repository (`https://nvidia.github.io/k8s-device-plugin`), or the static DaemonSet manifests under [`deployments/static/`](https://github.com/NVIDIA/k8s-device-plugin/tree/main/deployments/static)
- Container image: `nvcr.io/nvidia/k8s-device-plugin` (no longer published to Docker Hub since v0.12.0)

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
