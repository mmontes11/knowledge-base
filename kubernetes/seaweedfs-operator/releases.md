---
upstream: https://github.com/seaweedfs/seaweedfs-operator
last_updated: 2026-08-17
---

# seaweedfs-operator — releases

Latest 10 official releases (the `1.0.x` operator series), newest first. Check the ⚠️ entries before upgrading. Since 1.0.26 the operator has shipped with a uniform cadence: each tagged release bumps the [SeaweedFS](https://github.com/seaweedfs/seaweedfs) data-plane dependency (see the per-release compare links) rather than changing the operator API.

## 1.0.35 — 2026-08-17

[Release page](https://github.com/seaweedfs/seaweedfs-operator/releases/tag/1.0.35)

- SeaweedFS dependency: `v0.0.0-20260806065542-c01fe1493dc5` → `v0.0.0-20260817070407-5c43c03b76c0`. [Data-plane compare](https://github.com/seaweedfs/seaweedfs/compare/c01fe1493dc5...5c43c03b76c0)

## 1.0.34 — 2026-08-06

[Release page](https://github.com/seaweedfs/seaweedfs-operator/releases/tag/1.0.34)

- SeaweedFS dependency: `v0.0.0-20260719061107-eb6d8ebd5f68` → `v0.0.0-20260806065542-c01fe1493dc5`. [Data-plane compare](https://github.com/seaweedfs/seaweedfs/compare/eb6d8ebd5f68...c01fe1493dc5)

## 1.0.33 — 2026-07-20

[Release page](https://github.com/seaweedfs/seaweedfs-operator/releases/tag/1.0.33)

- SeaweedFS dependency: `v0.0.0-20260710040814-db42bb49757b` → `v0.0.0-20260719061107-eb6d8ebd5f68`. [Data-plane compare](https://github.com/seaweedfs/seaweedfs/compare/db42bb49757b...eb6d8ebd5f68)

## 1.0.32 — 2026-07-10

[Release page](https://github.com/seaweedfs/seaweedfs-operator/releases/tag/1.0.32)

- SeaweedFS dependency: `v0.0.0-20260706054851-65dff4a492df` → `v0.0.0-20260710040814-db42bb49757b`. [Data-plane compare](https://github.com/seaweedfs/seaweedfs/compare/65dff4a492df...db42bb49757b)

## 1.0.31 — 2026-07-06

[Release page](https://github.com/seaweedfs/seaweedfs-operator/releases/tag/1.0.31)

- SeaweedFS dependency: `v0.0.0-20260628053626-5797fb24ec89` → `v0.0.0-20260706054851-65dff4a492df`. [Data-plane compare](https://github.com/seaweedfs/seaweedfs/compare/5797fb24ec89...65dff4a492df)

## 1.0.30 — 2026-06-29

[Release page](https://github.com/seaweedfs/seaweedfs-operator/releases/tag/1.0.30)

- SeaweedFS dependency: `v0.0.0-20260625043821-3b9e196e5ff2` → `v0.0.0-20260628053626-5797fb24ec89`. [Data-plane compare](https://github.com/seaweedfs/seaweedfs/compare/3b9e196e5ff2...5797fb24ec89)

## 1.0.29 — 2026-06-25

[Release page](https://github.com/seaweedfs/seaweedfs-operator/releases/tag/1.0.29)

- SeaweedFS dependency: `v0.0.0-20260622185137-36f2ddcaea03` → `v0.0.0-20260625043821-3b9e196e5ff2`. [Data-plane compare](https://github.com/seaweedfs/seaweedfs/compare/36f2ddcaea03...3b9e196e5ff2)

## 1.0.28 — 2026-06-22

[Release page](https://github.com/seaweedfs/seaweedfs-operator/releases/tag/1.0.28)

- SeaweedFS dependency: `v0.0.0-20260622072450-eb939761665c` → `v0.0.0-20260622185137-36f2ddcaea03`. [Data-plane compare](https://github.com/seaweedfs/seaweedfs/compare/eb939761665c...36f2ddcaea03)

## 1.0.27 — 2026-06-22

[Release page](https://github.com/seaweedfs/seaweedfs-operator/releases/tag/1.0.27)

- SeaweedFS dependency: `v0.0.0-20260616184732-5e8152b81c70` → `v0.0.0-20260622072450-eb939761665c`. [Data-plane compare](https://github.com/seaweedfs/seaweedfs/compare/5e8152b81c70...eb939761665c)

## 1.0.26 — 2026-06-15

[Release page](https://github.com/seaweedfs/seaweedfs-operator/releases/tag/1.0.26)

- **IAM cross-namespace fix**: global IAM names are now scoped against cross-namespace collisions ([PR #279](https://github.com/seaweedfs/seaweedfs-operator/pull/279)).
- **gRPC client recovery**: recovers gRPC clients after Seaweed rollouts ([PR #281](https://github.com/seaweedfs/seaweedfs-operator/pull/281)).
- [Full changelog](https://github.com/seaweedfs/seaweedfs-operator/compare/1.0.25...1.0.26)

Notes:

- Two release series exist: the operator tags (`1.0.x`, listed above) and the **Helm chart** releases (`seaweedfs-operator-0.1.x`, e.g. `0.1.38` = appVersion `1.0.35`, `0.1.32` = appVersion `1.0.29`). Chart releases have empty release notes; chart versioning has decoupled from operator tags, so pick the chart by its `appVersion`.
- Since 1.0.26 no API or breaking changes have been announced in release notes; the operator API has been stable — but each release moves the SeaweedFS data-plane dependency forward, so review the linked data-plane compares when upgrading.
