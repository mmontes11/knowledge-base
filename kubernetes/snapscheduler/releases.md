---
upstream: https://github.com/backube/snapscheduler
last_updated: 2026-08-16
---

# snapscheduler — releases

Latest 10 official releases, newest first. Check the ⚠️ entries before upgrading; the upgrade note in v3.2.0 applies to any upgrade from 3.1.0 or earlier.

## v3.5.0 — 2025-05-14

[Release page](https://github.com/backube/snapscheduler/releases/tag/v3.5.0)

- **Optional owner references on snapshots**: `spec`-independent chart value `enableOwnerReferences` lets the operator set owner references on the snapshots it creates, so deleting a `SnapshotSchedule` cascade-deletes its snapshots. Disabled by default.

## v3.4.0 — 2024-05-09

[Release page](https://github.com/backube/snapscheduler/releases/tag/v3.4.0)

- Dependency updates, including CVE fixes; operator-sdk upgraded to 1.34.1.
- Helm chart: `metadata.namespace` added to namespaced resources for Argo CD compatibility.

## v3.3.0 — 2023-09-22

[Release page](https://github.com/backube/snapscheduler/releases/tag/v3.3.0)

- **Scheduling knobs via Helm**: `priorityClassName` for the operator, plus pod labels and annotations.
- CRON spec validation improved to allow "slash" notation.

## v3.2.0 — 2022-10-10

[Release page](https://github.com/backube/snapscheduler/releases/tag/v3.2.0)

- ⚠️ **Helm chart now owns the CRD**: from this version the chart installs/upgrades the `snapshotschedules.snapscheduler.backube` CRD itself. Upgrading from 3.1.0 or earlier fails unless the pre-existing CRD is labeled/annotated with Helm ownership first (`app.kubernetes.io/managed-by=Helm`, `meta.helm.sh/release-name=snapscheduler`, `meta.helm.sh/release-name`-matching namespace) — the release notes carry the exact `kubectl label`/`kubectl annotate` commands.
- Ability to pin a container image hash (not just a tag) when deploying via Helm.
- More permissive cronspec validation; kube-rbac-proxy upgraded to v0.13.1; operator-sdk upgraded to 1.23.0.

## v3.1.0 — 2022-08-01

[Release page](https://github.com/backube/snapscheduler/releases/tag/v3.1.0)

- **Topology spread constraints** for the operator configurable via Helm chart.
- kube-rbac-proxy upgraded to v0.13.0; operator-sdk upgraded to 1.22.0.

## v3.0.0 — 2022-04-01

[Release page](https://github.com/backube/snapscheduler/releases/tag/v3.0.0)

- ⚠️ Snapshots are now accessed via the `snapshot.storage.k8s.io/v1` API (GA version).
- ⚠️ **Removed support for Kubernetes < 1.20**.
- operator-sdk upgraded to 1.18.

## v2.1.0 — 2021-12-17

[Release page](https://github.com/backube/snapscheduler/releases/tag/v2.1.0)

- Helm chart: resource requests for the RBAC proxy container and choice of the kube-rbac-proxy image are now configurable.
- Built with Go 1.17; kube-rbac-proxy image upgraded to 0.11.0; operator-sdk upgraded to 1.15.

## v2.0.0 — 2021-08-03

[Release page](https://github.com/backube/snapscheduler/releases/tag/v2.0.0)

- ⚠️ CRD moved to `apiextensions.k8s.io/v1`.
- ⚠️ **Removed support for Kubernetes < 1.17** and for the `snapshot.storage.k8s.io/v1alpha1` snapshot API.
- ⚠️ Node selector labels switched off `beta.kubernetes.io/arch` / `beta.kubernetes.io/os` (to their GA equivalents) — nodes that only carry the beta labels will no longer be selected.
- Operator-sdk 1.10 scaffolding; default host anti-affinity for operator replicas.

## v1.2.0 — 2021-04-05

[Release page](https://github.com/backube/snapscheduler/releases/tag/v1.2.0)

- Operator base container switched to distroless.
- Fixed metrics being inaccessible from the `snapscheduler-metrics` Service.

## v1.1.1 — 2020-04-24

[Release page](https://github.com/backube/snapscheduler/releases/tag/v1.1.1)

- Fixed a crash when `snapshotTemplate` was not defined on a schedule.
