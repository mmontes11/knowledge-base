# HAMi Features

## Fractional GPU Allocation

- **gpucores**: Allocate a percentage of GPU compute resources
- **gpumem**: Allocate a specific amount of GPU memory (MiB)
- **gpumem-percentage**: Allocate GPU memory as a percentage
- Combines with standard `nvidia.com/gpu` for whole-GPU or fractional allocation

## MIG (Multi-Instance GPU)

- Dynamic MIG allocation and deallocation
- MIG mixed strategy (combine MIG and vGPU)
- Dynamic MIG reconfiguration during runtime

## Multi-Vendor Heterogeneous Accelerators

Supports 11+ accelerator vendors:

- NVIDIA GPU (vGPU, MIG)
- Ascend NPU (DRA-based)
- AMD Mi300x
- Biren GPU
- Enflame GCU
- Hygon DCU
- AWS Neuron (Inferentia/Trainium)
- Kunlunxin XPU
- Metax GPU
- VastAI GPU
- Iluvatar CoreX

## Scheduling

- **Scheduling policies**: mutex, binpack, spread, priority
- **Gang scheduling** (podgroup): all-or-nothing pod group scheduling
- **NUMA topology awareness**: schedule pods on same NUMA node as GPU
- **Device health tracking**: avoid scheduling to unhealthy devices
- **Init container support**: device allocation for init containers
- **Priority scheduling**: pod priority class aware scheduling

## Monitoring and Management

- **vGPU Monitor**: per-container GPU utilization metrics (Prometheus)
- **Resource enforcement**: CPU, memory, and GPU limits enforced at runtime
- **Prometheus metrics**: GPU utilization, memory usage, MIG status
- **WebUI**: dashboard for cluster GPU resource overview
- **Cluster Autoscaler integration**: GPU-aware node scaling

## Admission and Policy

- **Webhook**: admission controller for GPU resource validation
- **ResourceQuota integration**: per-namespace GPU quota enforcement
- **Namespace-level limits**: `hami.io/enable-vgpu-ctl` and `hami.io/gpumem-limit` annotations

## Dynamic Resource Allocation (DRA)

- DRA support for Ascend NPU
- DRA parameter management

## Multi-Tenancy

- Per-namespace GPU resource isolation
- GPU type specification (`nvidia.com/use-gputype`, `nvidia.com/nouse-gputype`)
- Custom resource names for multi-tenant environments
