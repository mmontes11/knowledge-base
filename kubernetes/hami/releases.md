# HAMi Releases

## v2.10.0

**Published**: 2026-08-21

- **General**: Removed DRA components from NVIDIA path
- **DRA**: Added Ascend DRA support
- **Scheduling**:
  - Init container support
  - PodGroup (gang scheduling)
  - Flexible MIG allocation
  - Mutex policy enhancement
  - Policy combination support
  - Handshake annotations
  - NUMA alignment
  - Supervisor improvements

## v2.9.0

**Published**: 2026-05-19

- Dynamic MIG (allocation and deallocation)
- Dynamic MIG reconfiguration
- vGPU monitor improvements
- WebUI updates
- Multi-vendor enhancements

## v2.8.3

**Published**: 2026-05-19

- Bug fixes and stability improvements
- Scheduler edge case handling
- Device plugin reliability fixes

## v2.8.2

**Published**: 2026-04-28

- MIG mixed strategy fixes
- vGPU monitor memory tracking corrections
- Helm chart updates

## v2.8.1

**Published**: 2026-04-17

- Scheduling policy bug fixes
- Device health check improvements
- Documentation updates

## v2.8.0

**Published**: 2026-01-20

- WebUI release
- Gang scheduling (podgroup) support
- Priority scheduling
- NUMA topology awareness
- Cluster Autoscaler support
- Multi-vendor: Kunlunxin, Metax, VastAI, Iluvatar
- DRA support groundwork
- Scheduler configuration externalization

## v2.7.1

**Published**: 2026-01-23

- Backport of critical fixes to 2.7.x series
- Stability and reliability patches

## v2.7.0

**Published**: 2025-09-26

- Webhook (admission controller)
- ResourceQuota integration
- WebUI alpha
- Multi-vendor: Hygon DCU, Biren, Enflame, AWS Neuron
- Device health tracking
- Scheduling policy: mutex
- `hami.io/device-health` label
- `nvidia.com/use-gputype` / `nvidia.com/nouse-gputype` annotations

## v2.6.1

**Published**: 2025-08-04

- vGPU monitor: Prometheus metrics endpoint
- Scheduler: device-aware score plugin
- MIG support: initial implementation
- Helm chart: initial release
- Multi-vendor: AMD Mi300x

## v2.5.3

**Published**: 2025-08-05

- Fractional GPU allocation (gpucores, gpumem, gpumem-percentage)
- Scheduler extender: filter/score/bind
- Device plugin: vGPU resource registration
- Namespace-level vGPU control
- `hami.io/enable-vgpu-ctl` annotation
