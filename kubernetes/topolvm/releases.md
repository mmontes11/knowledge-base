---
upstream: https://github.com/topolvm/topolvm
last_updated: 2026-09-03
---

# topolvm — releases

Latest 10 official releases, newest first. This repository publishes two kinds of releases interleaved on the same timeline: **Helm chart** releases (`topolvm-chart-v*`, shipping the chart) and **application** releases (`v*`, shipping the controller/node/scheduler images). Check the ⚠️ entries before upgrading.

## topolvm-chart-v17.2.0 — 2026-09-02 (Helm chart)

[Release page](https://github.com/topolvm/topolvm/releases/tag/topolvm-chart-v17.2.0)

- Version-bump release shipping the `0.41.1` application images: the default `image.reference` becomes `0.41.1@sha256:70548dbe…` ([#1226](https://github.com/topolvm/topolvm/pull/1226)).

## v0.41.1 — 2026-09-01 (application)

[Release page](https://github.com/topolvm/topolvm/releases/tag/v0.41.1)

- Patch release: bumped the `grpc`, `x/net`, and `otel` dependencies for security fixes ([#1220](https://github.com/topolvm/topolvm/pull/1220)).
- Release images are now pinned by digest to mitigate the impact of a compromised image registry ([#1196](https://github.com/topolvm/topolvm/pull/1196)).
- Removed outdated documentation of the removed PVC-deletion behavior ([#1214](https://github.com/topolvm/topolvm/pull/1214)).

## topolvm-chart-v17.1.0 — 2026-08-05 (Helm chart)

[Release page](https://github.com/topolvm/topolvm/releases/tag/topolvm-chart-v17.1.0)

- Option to preserve CRDs on uninstall ([#1206](https://github.com/topolvm/topolvm/pull/1206)).
- New `podAnnotations` values for the controller and node pods ([#1209](https://github.com/topolvm/topolvm/pull/1209)).

## topolvm-chart-v17.0.0 — 2026-07-09 (Helm chart)

[Release page](https://github.com/topolvm/topolvm/releases/tag/topolvm-chart-v17.0.0)

- ⚠️ **Breaking values change: `image.tag` was replaced by `image.reference`** (images now pinned by digest, [#1196](https://github.com/topolvm/topolvm/pull/1196)) — move any `image.tag` overrides to `image.reference` before upgrading.

## topolvm-chart-v16.1.1 — 2026-06-03 (Helm chart)

[Release page](https://github.com/topolvm/topolvm/releases/tag/topolvm-chart-v16.1.1)

- Aligned the `lvmd-socket-dir` hostPath `type` between the node pod and the `lvmd` DaemonSet ([#1180](https://github.com/topolvm/topolvm/pull/1180)).

## topolvm-chart-v16.1.0 — 2026-05-08 (Helm chart)

[Release page](https://github.com/topolvm/topolvm/releases/tag/topolvm-chart-v16.1.0)

- Kubernetes `1.35` support added ([#1173](https://github.com/topolvm/topolvm/pull/1173)).

## v0.41.0 — 2026-05-01 (application)

[Release page](https://github.com/topolvm/topolvm/releases/tag/v0.41.0)

- Kubernetes `1.35` support added ([#1173](https://github.com/topolvm/topolvm/pull/1173)).
- `lvmd/command` now uses `exec.CommandContext`, so cancelled gRPC calls kill abandoned LVM child processes ([#1172](https://github.com/topolvm/topolvm/pull/1172)).
- Fixed stdout handling in the LVM report parser (drain output before returning) ([#1171](https://github.com/topolvm/topolvm/pull/1171)).

## topolvm-chart-v16.0.1 — 2026-04-02 (Helm chart)

[Release page](https://github.com/topolvm/topolvm/releases/tag/topolvm-chart-v16.0.1)

- Version-bump release only — chart bumped to `16.0.1` with no substantive changes versus `topolvm-chart-v16.0.0` ([#1164](https://github.com/topolvm/topolvm/pull/1164)).

## v0.40.2 — 2026-04-02 (application)

[Release page](https://github.com/topolvm/topolvm/releases/tag/v0.40.2)

- ⚠️ Fixed a **volume expansion timeout for LVM extent-aligned sizes** ([#1146](https://github.com/topolvm/topolvm/pull/1146)) — affects online expansion requests that land exactly on extent boundaries.
- Bumped the gRPC dependency.

## topolvm-chart-v16.0.0 — 2026-03-11 (Helm chart)

[Release page](https://github.com/topolvm/topolvm/releases/tag/topolvm-chart-v16.0.0)

- ⚠️ **Removed the pod-erase-on-PVC-delete behavior** ([#1148](https://github.com/topolvm/topolvm/pull/1148)) — pods are no longer force-deleted when their PVC is deleted; automation that relied on that side effect must be adjusted.
- New `customCertSecret` value for external (cert-manager-less) certificate management ([#1142](https://github.com/topolvm/topolvm/pull/1142)); fixed a duplicate port-name warning ([#1147](https://github.com/topolvm/topolvm/pull/1147)).
