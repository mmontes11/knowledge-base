---
upstream: https://github.com/bitnami-labs/sealed-secrets
last_updated: 2026-08-18
---

# sealed-secrets — Releases

Latest 10 official application releases, newest first. The Helm chart ships on its own `helm-v2.x` tag series (interleaved in the release list), so chart upgrades are versioned separately from the controller. Full per-version notes: [RELEASE-NOTES.md](https://github.com/bitnami/sealed-secrets/blob/main/RELEASE-NOTES.md) and [releases on GitHub](https://github.com/bitnami/sealed-secrets/releases).

## v0.39.0 — 2026-08-18

[Release page](https://github.com/bitnami/sealed-secrets/releases/tag/v0.39.0)

- ⚠️ **Security fix**: the `/v1/verify` endpoint no longer behaves as a decryption oracle — [bitnami/sealed-secrets#2019](https://github.com/bitnami/sealed-secrets/pull/2019).
- New `--metrics-omit-secret-labels` flag: stops populating per-`SealedSecret` labels on condition metrics — [bitnami/sealed-secrets#1972](https://github.com/bitnami/sealed-secrets/pull/1972).
- New `hostUsers` Helm chart option for the controller deployment — [bitnami/sealed-secrets#1978](https://github.com/bitnami/sealed-secrets/pull/1978).
- Controller now starts its HTTP server early when given a large `--additional-namespace` list, fixing slow readiness — [bitnami/sealed-secrets#2018](https://github.com/bitnami/sealed-secrets/pull/2018).
- Dependency bumps: `golang.org/x/crypto` 0.54.0, `k8s.io/*` 0.36.3, `prometheus/client_golang` 1.24.1; `/v1/rotate` endpoint rate-limited ([#1971](https://github.com/bitnami/sealed-secrets/pull/1971)).

## v0.38.4 — 2026-07-03

[Release page](https://github.com/bitnami/sealed-secrets/releases/tag/v0.38.4)

- Incomplete release cut to work around publisher credential problems; no functional changes.

## v0.38.3 — 2026-07-03

[Release page](https://github.com/bitnami/sealed-secrets/releases/tag/v0.38.3)

- Incomplete release cut to work around publisher credential problems; no functional changes.

## v0.38.2 — 2026-07-03

[Release page](https://github.com/bitnami/sealed-secrets/releases/tag/v0.38.2)

- Artifact Hub repository metadata publishing for verified-publisher status and Artifact Hub badges added to the README ([#2000](https://github.com/bitnami/sealed-secrets/pull/2000), [#1999](https://github.com/bitnami/sealed-secrets/pull/1999)).
- Dependency bumps: `k8s.io/api`/`k8s.io/client-go` 0.36.2, ginkgo/gomega, code-generator 0.36.2.

## v0.38.1 — 2026-06-18

[Release page](https://github.com/bitnami/sealed-secrets/releases/tag/v0.38.1)

- Incomplete release cut to work around publisher credential problems; no functional changes.

## v0.38.0 — 2026-06-18

[Release page](https://github.com/bitnami/sealed-secrets/releases/tag/v0.38.0)

- ⚠️ **Migration**: the project **moved from the `bitnami-labs` to the `bitnami` GitHub org** — [bitnami/sealed-secrets#1983](https://github.com/bitnami/sealed-secrets/pull/1983). The old `bitnami-labs/sealed-secrets` URLs redirect; pin references, image digests, CI sources, and documentation links to `bitnami/sealed-secrets`.
- **ppc64le image support** for the controller — [bitnami/sealed-secrets#1973](https://github.com/bitnami/sealed-secrets/pull/1973).
- **Default `PrometheusRule` in the Helm chart** alerting on out-of-sync `SealedSecret`s — [bitnami/sealed-secrets#1980](https://github.com/bitnami/sealed-secrets/pull/1980).
- **Security-context defaults now conform to the `restricted` Pod Security Standard** — [bitnami/sealed-secrets#1981](https://github.com/bitnami/sealed-secrets/pull/1981): clusters whose PSP/PSS settings are looser than before should re-check the controller deployment.
- Fixed a data race in the key registry (mutex locking) — [bitnami/sealed-secrets#1905](https://github.com/bitnami/sealed-secrets/pull/1905).
- Go 1.26.4; chart publishing migrated to a new OCI registry.

## v0.37.0 — 2026-05-21

[Release page](https://github.com/bitnami/sealed-secrets/releases/tag/v0.37.0)

- **Plaintext `template.data` values are now exposed to the `template` render context**, so a sealed key can derive other keys from decrypted values — [bitnami/sealed-secrets#1940](https://github.com/bitnami/sealed-secrets/pull/1940).
- Go 1.26.3; `k8s.io/*` client libraries 0.36.1.
- Cooldown added to the dependency-update schedule and the Kubernetes compatibility test matrix (#1955).

## v0.36.6 — 2026-04-09

[Release page](https://github.com/bitnami/sealed-secrets/releases/tag/v0.36.6)

- Incomplete release cut to work around Docker Hub publisher credential problems; no functional changes.

## v0.36.5 — 2026-04-09

[Release page](https://github.com/bitnami/sealed-secrets/releases/tag/v0.36.5)

- Incomplete release cut to work around Docker Hub publisher credential problems; no functional changes.

## v0.36.4 — 2026-04-09

[Release page](https://github.com/bitnami/sealed-secrets/releases/tag/v0.36.4)

- CI hygiene only: Kubernetes integration-test matrix bumped to the latest 1.33/1.34/1.35 patch releases — [bitnami/sealed-secrets#1935](https://github.com/bitnami/sealed-secrets/pull/1935).
