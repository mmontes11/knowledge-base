---
upstream: https://github.com/seaweedfs/seaweedfs-operator
last_updated: 2026-09-03
---

# seaweedfs-operator — releases

Latest 10 official releases (the `1.0.x` operator series), newest first. Check the ⚠️ entries before upgrading. Since 1.0.26 the operator has shipped with a uniform cadence: each tagged release bumps the [SeaweedFS](https://github.com/seaweedfs/seaweedfs) data-plane dependency (see the per-release compare links); since 1.0.36 it has also gained additive `Seaweed` spec fields — see the per-release entries.

## 1.0.37 — 2026-09-01

[Release page](https://github.com/seaweedfs/seaweedfs-operator/releases/tag/1.0.37)

- **Master persistence**: new `spec.master.persistence` mounts a volume for the master's `-mdir` (raft log, snapshots, cluster `TopologyId`) so cluster identity survives a full restart; off by default, and a shared `existingClaim` is rejected at admission for multi-replica masters. [README](https://github.com/seaweedfs/seaweedfs-operator/blob/1.0.37/README.md#key-fields-explained)
- **Lance Namespace API**: new `spec.filer.lance` serves a Lance Namespace REST API on the filer — **on by default**, port 9101 — with a worker-lance sidecar on fixed port 9328; `spec.worker.metricsPort` is rejected at admission when set to 9328 while Lance is on. [API types](https://github.com/seaweedfs/seaweedfs-operator/blob/1.0.37/api/v1/seaweed_types.go)
- SeaweedFS dependency: `v0.0.0-20260821064632-8a532cc0cffd` → `v0.0.0-20260831232101-79b87202136c` (release notes are bare; version read from [go.mod at the tag](https://github.com/seaweedfs/seaweedfs-operator/blob/1.0.37/go.mod)). [Data-plane compare](https://github.com/seaweedfs/seaweedfs/compare/8a532cc0cffd...79b87202136c)

## 1.0.36 — 2026-08-21

[Release page](https://github.com/seaweedfs/seaweedfs-operator/releases/tag/1.0.36)

- ⚠️ **Behavior change**: `[jwt.filer_signing]` is no longer rendered into `security.toml` unconditionally — filer write-signing is now opt-in via `spec.securityConfig.jwtSigning.filerWrite: true`; set it when upgrading if you relied on the old default. [README — JWT Signing](https://github.com/seaweedfs/seaweedfs-operator/blob/1.0.36/README.md#jwt-signing-securitytoml)
- **JWT signing (security.toml)**: new `spec.securityConfig.jwtSigning` turns per-section token enforcement on for volume/filer writes and reads (`volumeWrite`, `volumeRead`, `filerWrite`, `filerRead`, `expiresAfterSeconds`); HMAC keys are generated per section, kept stable in a `<name>-security-config` Secret, and inherited by backup/mirror jobs. [README — JWT Signing](https://github.com/seaweedfs/seaweedfs-operator/blob/1.0.36/README.md#jwt-signing-securitytoml)
- **Listener bind addresses**: new `ipBind` on master, volume servers, and filer (default `0.0.0.0`) removes the cold-start CoreDNS race that made every component restart once at boot. [README — Key fields explained](https://github.com/seaweedfs/seaweedfs-operator/blob/1.0.36/README.md#key-fields-explained)
- **S3 credentials rotation**: changing an adopted `S3Credentials` `Secret` now rotates the identity — a new access key is registered and the superseded pair revoked. [README — Declarative IAM](https://github.com/seaweedfs/seaweedfs-operator/blob/1.0.36/README.md#declarative-iam-identities-credentials-policies)
- SeaweedFS dependency: `v0.0.0-20260817070407-5c43c03b76c0` → `v0.0.0-20260821064632-8a532cc0cffd`. [Data-plane compare](https://github.com/seaweedfs/seaweedfs/compare/5c43c03b76c0...8a532cc0cffd)

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

Notes:

- Two release series exist: the operator tags (`1.0.x`, listed above) and the **Helm chart** releases (`seaweedfs-operator-0.1.x`, e.g. `0.1.38` = appVersion `1.0.35`, `0.1.32` = appVersion `1.0.29`). Chart releases have empty release notes; chart versioning has decoupled from operator tags, so pick the chart by its `appVersion`.
- Upstream release notes are minimal — 1.0.36 and 1.0.37 shipped real `Seaweed` API and behavior changes with bare notes — so check the per-release entries above and review the linked data-plane compares when upgrading.
