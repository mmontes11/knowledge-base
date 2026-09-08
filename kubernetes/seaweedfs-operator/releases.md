---
upstream: https://github.com/seaweedfs/seaweedfs-operator
last_updated: 2026-09-08
---

# seaweedfs-operator — releases

Latest 10 official releases, newest first. The repository publishes two release lines interleaved on the same timeline: **operator** releases (`1.0.x`, shipping the operator image) and **Helm chart** releases (`seaweedfs-operator-0.1.x`, packaging the [`deploy/helm`](https://github.com/seaweedfs/seaweedfs-operator/tree/master/deploy/helm) chart as a `.tgz` release asset). Chart releases are fully automated: every `1.0.x` tag push triggers the [chart workflow](https://github.com/seaweedfs/seaweedfs-operator/blob/master/.github/workflows/helm_chart_release.yml), which sets the chart `appVersion` to that operator tag, bumps the chart patch, and publishes the chart a few minutes later — so each chart release pairs with the operator tag published right before it, with a constant patch offset of 3 (`0.1.41` = `appVersion 1.0.38`, per the [Helm repo index](https://github.com/seaweedfs/seaweedfs-operator/blob/gh-pages/helm/index.yaml)). The chart is installed from `https://seaweedfs.github.io/seaweedfs-operator/` ([upstream README](https://github.com/seaweedfs/seaweedfs-operator/blob/master/README.md#helm)); when picking a chart version, match it by `appVersion`, not by its own version number. Check the ⚠️ entries before upgrading.

Since 1.0.26 the operator has shipped with a uniform cadence: each tagged release bumps the [SeaweedFS](https://github.com/seaweedfs/seaweedfs) data-plane dependency (see the per-release compare links); since 1.0.36 it has also gained additive `Seaweed` spec fields — see the per-release entries.

## seaweedfs-operator-0.1.41 — 2026-09-08 (Helm chart)

[Release page](https://github.com/seaweedfs/seaweedfs-operator/releases/tag/seaweedfs-operator-0.1.41)

- Version-bump release: chart `0.1.41` ships `appVersion: 1.0.38` ([Helm repo index](https://github.com/seaweedfs/seaweedfs-operator/blob/gh-pages/helm/index.yaml)); chart releases carry no release notes.

## 1.0.38 — 2026-09-08 (operator)

[Release page](https://github.com/seaweedfs/seaweedfs-operator/releases/tag/1.0.38)

- SeaweedFS dependency: `v0.0.0-20260831232101-79b87202136c` → `v0.0.0-20260908034521-d997fba15755`. [Data-plane compare](https://github.com/seaweedfs/seaweedfs/compare/79b87202136c...d997fba15755)

## seaweedfs-operator-0.1.40 — 2026-09-01 (Helm chart)

[Release page](https://github.com/seaweedfs/seaweedfs-operator/releases/tag/seaweedfs-operator-0.1.40)

- Version-bump release: chart `0.1.40` ships `appVersion: 1.0.37` ([Helm repo index](https://github.com/seaweedfs/seaweedfs-operator/blob/gh-pages/helm/index.yaml)); chart releases carry no release notes.

## 1.0.37 — 2026-09-01 (operator)

[Release page](https://github.com/seaweedfs/seaweedfs-operator/releases/tag/1.0.37)

- **Master persistence**: new `spec.master.persistence` mounts a volume for the master's `-mdir` (raft log, snapshots, cluster `TopologyId`) so cluster identity survives a full restart; off by default, and a shared `existingClaim` is rejected at admission for multi-replica masters. [README](https://github.com/seaweedfs/seaweedfs-operator/blob/1.0.37/README.md#key-fields-explained)
- **Lance Namespace API**: new `spec.filer.lance` serves a Lance Namespace REST API on the filer — **on by default**, port 9101 — with a worker-lance sidecar on fixed port 9328; `spec.worker.metricsPort` is rejected at admission when set to 9328 while Lance is on. [API types](https://github.com/seaweedfs/seaweedfs-operator/blob/1.0.37/api/v1/seaweed_types.go)
- SeaweedFS dependency: `v0.0.0-20260821064632-8a532cc0cffd` → `v0.0.0-20260831232101-79b87202136c` (release notes are bare; version read from [go.mod at the tag](https://github.com/seaweedfs/seaweedfs-operator/blob/1.0.37/go.mod)). [Data-plane compare](https://github.com/seaweedfs/seaweedfs/compare/8a532cc0cffd...79b87202136c)

## seaweedfs-operator-0.1.39 — 2026-08-21 (Helm chart)

[Release page](https://github.com/seaweedfs/seaweedfs-operator/releases/tag/seaweedfs-operator-0.1.39)

- Version-bump release: chart `0.1.39` ships `appVersion: 1.0.36` ([Helm repo index](https://github.com/seaweedfs/seaweedfs-operator/blob/gh-pages/helm/index.yaml)); chart releases carry no release notes.

## 1.0.36 — 2026-08-21 (operator)

[Release page](https://github.com/seaweedfs/seaweedfs-operator/releases/tag/1.0.36)

- ⚠️ **Behavior change**: `[jwt.filer_signing]` is no longer rendered into `security.toml` unconditionally — filer write-signing is now opt-in via `spec.securityConfig.jwtSigning.filerWrite: true`; set it when upgrading if you relied on the old default. [README — JWT Signing](https://github.com/seaweedfs/seaweedfs-operator/blob/1.0.36/README.md#jwt-signing-securitytoml)
- **JWT signing (security.toml)**: new `spec.securityConfig.jwtSigning` turns per-section token enforcement on for volume/filer writes and reads (`volumeWrite`, `volumeRead`, `filerWrite`, `filerRead`, `expiresAfterSeconds`); HMAC keys are generated per section, kept stable in a `<name>-security-config` Secret, and inherited by backup/mirror jobs. [README — JWT Signing](https://github.com/seaweedfs/seaweedfs-operator/blob/1.0.36/README.md#jwt-signing-securitytoml)
- **Listener bind addresses**: new `ipBind` on master, volume servers, and filer (default `0.0.0.0`) removes the cold-start CoreDNS race that made every component restart once at boot. [README — Key fields explained](https://github.com/seaweedfs/seaweedfs-operator/blob/1.0.36/README.md#key-fields-explained)
- **S3 credentials rotation**: changing an adopted `S3Credentials` `Secret` now rotates the identity — a new access key is registered and the superseded pair revoked. [README — Declarative IAM](https://github.com/seaweedfs/seaweedfs-operator/blob/1.0.36/README.md#declarative-iam-identities-credentials-policies)
- SeaweedFS dependency: `v0.0.0-20260817070407-5c43c03b76c0` → `v0.0.0-20260821064632-8a532cc0cffd`. [Data-plane compare](https://github.com/seaweedfs/seaweedfs/compare/5c43c03b76c0...8a532cc0cffd)

## seaweedfs-operator-0.1.38 — 2026-08-17 (Helm chart)

[Release page](https://github.com/seaweedfs/seaweedfs-operator/releases/tag/seaweedfs-operator-0.1.38)

- Version-bump release: chart `0.1.38` ships `appVersion: 1.0.35` ([Helm repo index](https://github.com/seaweedfs/seaweedfs-operator/blob/gh-pages/helm/index.yaml)); chart releases carry no release notes.

## 1.0.35 — 2026-08-17 (operator)

[Release page](https://github.com/seaweedfs/seaweedfs-operator/releases/tag/1.0.35)

- SeaweedFS dependency: `v0.0.0-20260806065542-c01fe1493dc5` → `v0.0.0-20260817070407-5c43c03b76c0`. [Data-plane compare](https://github.com/seaweedfs/seaweedfs/compare/c01fe1493dc5...5c43c03b76c0)

## seaweedfs-operator-0.1.37 — 2026-08-06 (Helm chart)

[Release page](https://github.com/seaweedfs/seaweedfs-operator/releases/tag/seaweedfs-operator-0.1.37)

- Version-bump release: chart `0.1.37` ships `appVersion: 1.0.34` ([Helm repo index](https://github.com/seaweedfs/seaweedfs-operator/blob/gh-pages/helm/index.yaml)); chart releases carry no release notes.

## 1.0.34 — 2026-08-06 (operator)

[Release page](https://github.com/seaweedfs/seaweedfs-operator/releases/tag/1.0.34)

- SeaweedFS dependency: `v0.0.0-20260719061107-eb6d8ebd5f68` → `v0.0.0-20260806065542-c01fe1493dc5`. [Data-plane compare](https://github.com/seaweedfs/seaweedfs/compare/eb6d8ebd5f68...c01fe1493dc5)

Notes:

- Upstream release notes are minimal — 1.0.36 and 1.0.37 shipped real `Seaweed` API and behavior changes with bare notes — so check the per-release entries above and review the linked data-plane compares when upgrading.
