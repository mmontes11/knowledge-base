---
upstream: https://github.com/kubernetes-sigs/lws
last_updated: 2026-08-22
---

# lws — releases

Latest 10 official releases, most recent first. Check the ⚠️ entries before upgrading. Full notes are maintained at the [GitHub releases](https://github.com/kubernetes-sigs/lws/releases).

## v0.10.0 — 2026-08-11

[Release page](https://github.com/kubernetes-sigs/lws/releases/tag/v0.10.0)

- **`DisaggregatedSetRoleScaler` (KEP-849)**: per-role autoscaling — the controller auto-creates a `/scale`-exposing scaler for every `DisaggregatedSet` role with `scaling.mode: External`, so HPA/KEDA can write `spec.replicas` against a single role.
- **`DisaggregatedSet.spec.slices`** (independent copies of the whole role topology that roll out independently — changing it scales copies, not a rollout) and **`spec.placementPolicy`** (topology-aware co-location of a slice's roles / spread of slices across topology domains, injected as pod affinity).
- DisaggregatedSet documentation and API reference completed; admission webhook tightened to reject `LeaderWorkerSet.spec.replicas` outside `[0, 1000000]` and to reject `maxSurge=0 + maxUnavailable=0` on `DisaggregatedSet`. Helm chart fixes for cert-manager CA injection and the Prometheus `ServiceMonitor`.

## v0.9.0 — 2026-06-17

[Release page](https://github.com/kubernetes-sigs/lws/releases/tag/v0.9.0)

- **`RecreateGroupAfterStart` graduated to a first-class API field** (`spec.leaderWorkerTemplate.restartPolicy`) — on 0.8 it was an experimental annotation, so move annotation-based configs to the field.
- **`DisaggregatedSet` (KEP-766)** landed: a multi-role workload that manages one `LeaderWorkerSet` per role with cross-role rolling updates, plus its controller and Helm chart.
- ⚠️ Kubernetes dependencies upgraded to v0.35/v0.36 and the webhook migrated to the generic (controller-runtime 0.23) APIs; `LeaderWorkerSet` gained `status.observedGeneration`.

## v0.8.0 — 2026-01-26

[Release page](https://github.com/kubernetes-sigs/lws/releases/tag/v0.8.0)

- **`volumeClaimTemplates` + `persistentVolumeClaimRetentionPolicy`** added to `LeaderWorkerSetSpec` (KEP-622): leader/worker `StatefulSet`s can now declare per-group persistent volumes.
- **`RecreateGroupAfterStart`** introduced as an experimental annotation (`leaderworkerset.sigs.k8s.io/experimental-recreate-group-after-start`) — recreate a group only when no pods are pending, so large image pulls finish uninterrupted.
- RollingUpdate `maxUnavailable` graduated to Beta; scheduler features (including topology-aware scheduling) can be enabled via annotations; startup-policy section added to the docs.

## v0.7.0 — 2025-08-05

[Release page](https://github.com/kubernetes-sigs/lws/releases/tag/v0.7.0)

- **Partitioned rollouts (KEP-511)**: RollingUpdate now supports `spec.rolloutStrategy.rollingUpdateConfiguration.partition` for staged updates.
- **In-place `size` changes (KEP-552)**: `spec.leaderWorkerTemplate.size` can be updated on an existing group to add/remove workers without rebuilding it.
- **Gang scheduling (KEP-407)** added, and meaningful `kubectl` printer columns added to the `LeaderWorkerSet` CRD.

## v0.6.3 — 2025-08-06

[Release page](https://github.com/kubernetes-sigs/lws/releases/tag/v0.6.3)

- Helm fix backported to the 0.6 line: the chart now references the main `lws` image tag instead of the staging image.

## v0.6.2 — 2025-06-16

[Release page](https://github.com/kubernetes-sigs/lws/releases/tag/v0.6.2)

- Helm fix: the webhook `Service` selector could select unexpected pods when the `lws` chart was introduced as a dependency of another chart.

## v0.6.1 — 2025-04-15

[Release page](https://github.com/kubernetes-sigs/lws/releases/tag/v0.6.1)

- Fixed `UpdateInProgress` condition reporting for newly created `LeaderWorkerSet`s.

## v0.6.0 — 2025-03-30

[Release page](https://github.com/kubernetes-sigs/lws/releases/tag/v0.6.0)

- ⚠️ **Component configuration introduced** via the new `Configuration` CRD / `--config` file; passing CLI flags directly is deprecated in favor of the config file.
- **`LeaderExcluded` SubGroup (KEP-257)**: worker-only subgroups that exclude the leader pod.
- `LWS_WORKER_INDEX` environment variable injected into pods; controller image published multi-platform.

## v0.5.1 — 2025-02-03

[Release page](https://github.com/kubernetes-sigs/lws/releases/tag/v0.5.1)

- Backported fixes: removed the UPDATE operation from the pod mutating webhook and added nil-revision handling in the pod controller.

## v0.5.0 — 2025-01-08

[Release page](https://github.com/kubernetes-sigs/lws/releases/tag/v0.5.0)

- **ControllerRevision (KEP-238)**: each template revision is now recorded as a `ControllerRevision`, enabling revision tracking of `LeaderWorkerSet`s.
- The `kube-rbac-proxy` metrics sidecar was replaced (metrics served directly) and the first `lws` Helm chart shipped.
- Installable into an arbitrary (non-default) namespace; `TPU_NAME` environment variable injection for vLLM TPU multi-host.
