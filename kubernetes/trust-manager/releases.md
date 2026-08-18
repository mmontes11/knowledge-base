---
upstream: https://github.com/cert-manager/trust-manager
last_updated: 2026-08-16
---

# trust-manager — releases

Latest 10 official releases, newest first. Check the ⚠️ entries before upgrading.

## v0.24.0 — 2026-06-27

[Release page](https://github.com/cert-manager/trust-manager/releases/tag/v0.24.0)

- **Helm chart security contexts**: pod and container `securityContext` can now be configured. ⚠️ Possibly breaking: the `app.securityContext.seccompProfileEnabled` value was removed and superseded by the new mechanism.
- **ConfigMap `binaryData` sources**: `Bundle` sources can now read CA data from a ConfigMap's `binaryData` field.
- **Deterministic trust bundle hash**: fixed a non-deterministic `TrustBundleHash` that caused unnecessary reconciles when labels/annotations were set on target objects; bundle read access was also consolidated into the `cluster-reader` ClusterRole.

## v0.23.0 — 2026-06-10

[Release page](https://github.com/cert-manager/trust-manager/releases/tag/v0.23.0)

- **Debian Trixie trust package is now the default** (`trust-pkg-debian-trixie`) emitted by the Helm chart; the older Bullseye/Bookworm trust packages were re-released to fix scanner-reported vulnerabilities.
- Webhook TLS certificate duration can now be overridden; the umbrella-chart image-helper rename fix resolves a conflict with cert-manager's chart.
- Chart fixes for non-default values (`serviceAccount.name` override honored, ServiceMonitor `labels` rendered in `metadata.labels`).

## v0.22.1 — 2026-04-17

[Release page](https://github.com/cert-manager/trust-manager/releases/tag/v0.22.1)

- Patch release: dependency, Go, and base image bumps to address scanner-reported vulnerabilities.
- Non-user-facing changes preparing the `Bundle` → `ClusterBundle` migration, including a fix of a flaw in the `ClusterBundle` API.

## v0.22.0 — 2026-03-09

[Release page](https://github.com/cert-manager/trust-manager/releases/tag/v0.22.0)

- **Image mirroring**: new Helm values `imageRegistry`/`imageNamespace` make it easy to mirror the trust-manager container image to a self-hosted registry.
- Fixes CVE-2026-27138, CVE-2026-27137, CVE-2026-27142, and CVE-2026-25679.

## v0.21.1 — 2026-02-20

[Release page](https://github.com/cert-manager/trust-manager/releases/tag/v0.21.1)

- Patch release: fixes an RBAC regression introduced in v0.21.0 by granting the additional RBAC needed for the old `events` API group.

## v0.21.0 — 2026-02-19

[Release page](https://github.com/cert-manager/trust-manager/releases/tag/v0.21.0)

- **Filter non-CA certificates**: new Helm value `.filterNonCACerts.enabled` filters non-CA certificates out of CA sources based on the X.509 `basicConstraints` `isCa` flag (off by default).
- **Stricter `ClusterBundle` CRD**: tightened to pass newly enabled Kube API Linter checks — `sources` renamed to `sourceRefs`, and the bare `includeDefaultCAs` boolean replaced by the `defaultCAs` provider field (see [docs/alphav2-changes.md](https://github.com/cert-manager/trust-manager/blob/main/docs/alphav2-changes.md)).
- Primarily a fix for CVE-2025-68121; also adds ServiceMonitor relabeling support to the chart and sets the webhook Certificate private key rotation policy explicitly to `Always`.

## v0.20.3 — 2025-12-10

[Release page](https://github.com/cert-manager/trust-manager/releases/tag/v0.20.3)

- Dependency-bump release fixing scanner-reported vulnerabilities, notably CVE-2025-61729 (Go stdlib).

## v0.20.2 — 2025-10-16

[Release page](https://github.com/cert-manager/trust-manager/releases/tag/v0.20.2)

- Go upgrade `1.25.1` → `1.25.3`, fixing nine Go CVEs (CVE-2025-61724, CVE-2025-58187, CVE-2025-47912, CVE-2025-58183, CVE-2025-61723, CVE-2025-58186, CVE-2025-58185, CVE-2025-58188, CVE-2025-61725).
- ⚠️ Context for the v0.20.x line: this follows the X.509 issue cycle of v0.20.0/v0.20.1 (Go 1.25.2 regression) — if upgrading from earlier releases, v0.20.1+ is a safe entry point.

## v0.20.1 — 2025-10-10

[Release page](https://github.com/cert-manager/trust-manager/releases/tag/v0.20.1)

- ⚠️ **Fix for v0.20.0 known issue**: Go downgraded `1.25.2` → `1.25.1` to work around a backward-incompatible change to Go's X.509 verification (golang/go#75828) that affected trust-manager in v0.20.0.

## v0.20.0 — 2025-10-09

[Release page](https://github.com/cert-manager/trust-manager/releases/tag/v0.20.0)

- **Target namespace list**: the operator can now be restricted to an explicit list of target namespaces.
- ⚠️ **Known issue**: a backward-incompatible X.509 change in Go 1.25.2 (golang/go#75828) caused a known issue in this version — upgrade to v0.20.1 or later.
- ⚠️ **Removal**: the client-side → server-side apply migration code was removed; upgrade from very old versions incrementally.
