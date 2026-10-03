# HAMi API Reference

## Architecture

HAMi consists of three main components:

| Component | Location | Description |
|-----------|----------|-------------|
| Scheduler Extender | `cmd/scheduler/` | HTTP server implementing device-aware filter/score/bind for GPU scheduling |
| Device Plugin | `cmd/device-plugin/nvidia/` | K8s device plugin registering vGPU resources with kubelet |
| vGPU Monitor | `cmd/vGPUmonitor/` | DaemonSet providing per-container GPU monitoring and resource limit enforcement |

## Extended Resources

HAMi extends the standard `nvidia.com/gpu` resource with fractional allocation resources:

| Resource | Description | Example |
|----------|-------------|---------|
| `nvidia.com/gpu` | Whole GPU allocation (standard) | `1` |
| `nvidia.com/gpucores` | GPU compute cores (percentage of total) | `50` (50%) |
| `nvidia.com/gpumem` | GPU memory in MiB | `8192` (8 GiB) |
| `nvidia.com/gpumem-percentage` | GPU memory as percentage | `50` (50%) |

### Pod Request Example

```yaml
resources:
  limits:
    nvidia.com/gpu: "1"
    nvidia.com/gpucores: "50"
    nvidia.com/gpumem: "8192"
```

## Annotations and Labels

| Name | Scope | Description |
|------|-------|-------------|
| `nvidia.com/use-gputype` | Pod annotation | Request specific GPU type (e.g., `A100`) |
| `nvidia.com/nouse-gputype` | Pod annotation | Exclude specific GPU types |
| `hami.io/enable-vgpu-ctl` | Namespace annotation | Enable vGPU control for the namespace |
| `hami.io/gpumem-limit` | Namespace annotation | Maximum GPU memory limit for the namespace |
| `hami.io/scheduler-config` | Pod annotation | Override scheduler configuration for specific pods |
| `hami.io/device-health` | Node label | Device health status |
| `hami.io/topology-nodes` | Node label | NUMA topology information |

## Scheduling Policies

Configured via scheduler configuration (Helm value `scheduler.config`):

| Policy | Description |
|--------|-------------|
| `mutex` | Prevent multiple pods from sharing the same GPU |
| `binpack` | Pack pods onto fewer GPUs |
| `spread` | Spread pods across GPUs |
| `priority` | Priority-based scheduling |

## Multi-Vendor Device Types

HAMi supports heterogeneous accelerators beyond NVIDIA:

| Vendor | Resource Prefix | Notes |
|--------|----------------|-------|
| NVIDIA | `nvidia.com/` | Full vGPU support, MIG |
| Ascend | `huawei.com/` | DRA-based, `ascend.com/npu` |
| AMD (Mi300x) | `amd.com/` | Mi300x GPU |
| Biren | `biren.com/` | Biren GPUs |
| Enflame | `enflame.com/` | Enflame GCU |
| Hygon | `hygon.com/` | Hygon DCU |
| AWS Neuron | `aws.com/` | AWS Inferentia/Trainium |
| Kunlunxin | `kunlunxin.com/` | Kunlunxin XPU |
| Metax | `metax.com/` | Metax GPUs |
| VastAI | `vastai.com/` | VastAI GPUs |
| Iluvatar | `iluvatar.com/` | Iluvatar CoreX |

## Helm Chart Values

Key configuration values for the `charts/hami/` Helm chart:

| Value | Default | Description |
|-------|---------|-------------|
| `scheduler.replicas` | `1` | Scheduler extender replicas |
| `devicePlugin.enabled` | `true` | Enable device plugin |
| `vgpuMonitor.enabled` | `true` | Enable vGPU monitor |
| `webhook.enabled` | `false` | Enable admission webhook |
| `webui.enabled` | `false` | Enable WebUI |
| `scheduler.config.schedulingPolicy` | `default` | Scheduling policy |
