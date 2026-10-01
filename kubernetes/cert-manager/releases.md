---
upstream: https://github.com/jetstack/cert-manager
last_updated: 2026-10-01
---

# cert-manager — releases

Latest 10 official releases, newest first (alpha/beta releases omitted). See the [GitHub releases](https://github.com/jetstack/cert-manager/releases) for full notes.

## v1.21.2 — 2026-09-11

[Release page](https://github.com/jetstack/cert-manager/releases/tag/v1.21.2) — all users should upgrade.

- Fixes controller and webhook panics: the certificates-issuing controller panicked on `CertificateRequest`s with a failure time but no `Ready` condition; the validating webhook panicked on `AdmissionReview` requests missing optional fields (requests with an unset/mismatched resource are now denied instead of silently allowed, and validation is also enforced for equivalent-converted requests on non-v1 API versions).
- Stops the ACME and Vault issuers reflecting untrusted HTTP response bodies into `Issuer`/`Challenge` status conditions and Events (only ACME problem documents are surfaced, ACME response bodies capped at 16 MiB, other responses reported by HTTP status code alone) — prevents disclosure of internal response contents and unbounded-body DoS.
- **Vault**: a namespaced `Issuer` no longer authenticates with the controller's ambient AWS credentials for AWS IAM auth unless explicitly enabled via `--issuer-ambient-credentials` (`ClusterIssuer` and explicit `serviceAccountRef`/IRSA are unaffected).
- Other fixes: `dnsNames` de-duplication when multiple Gateway/ListenerSet listeners share a Secret, renewal windows with February-29 cron schedules across non-leap century years, HTTP-01 solver cleanup no longer failing with NotFound for already-deleted Ingress/pod/Service, `replaces` populated for the wrong issuer on issuer changes, and data races (HTTP-01 self-check with custom DNS servers, scheduler timer cleanup).
- Dependency bumps fixing reported vulnerabilities: Go 1.26.8, grpc 1.83.2, x/crypto 0.56.0.

## v1.21.1 — 2026-07-29

[Release page](https://github.com/jetstack/cert-manager/releases/tag/v1.21.1) — all users should upgrade.

- Fixes a controller **panic** for Certificates with `spec.renewal.policy: Disabled` (regression in 1.21.0 that caused crash-loops).
- Fixes `Issuer`/`ClusterIssuer` stuck at `Ready=False` (`InvalidSolver`) when the referenced ACME DNS-01 solver Secret is created *after* the Issuer (eager-validation regression in 1.21.0).
- Fixes log spam and dropped Secret informer events caused by a generics regression in 1.21.0.
- Fixes the commented Gateway API example in the Helm chart values (`gatewayAPI.enable` → `gatewayAPI.enabled`).
- Dependency bumps fixing reported vulnerabilities: grpc 1.82.1, cel-go 0.29.0, otel 1.44.0, x/text 0.40.0.

## v1.21.0 — 2026-07-08

[Release page](https://github.com/jetstack/cert-manager/releases/tag/v1.21.0) — features + three breaking Helm/RBAC changes.

- **ACME Renewal Information (ARI)** (RFC 9773) behind the `ACMEUseARI` feature gate; `waitInsteadOfSelfCheck` solver option for split-horizon/NAT environments; AWS IAM authentication for the Vault issuer (IRSA, Pod Identity, ambient credentials).
- **Certificate**: new `renewalPolicies` field; configurable max backoff for failed `CertificateRequest`s; Modern2026 PKCS#12 FIPS profile; fix for `renewBeforePercentage` integer overflow on ~3-year+ durations.
- **Gateway API**: HTTP-01 ListenerSet `parentRef` fallback annotation, `cert-manager.io/ignore-tls-listeners` annotation, additional listener protocols; `enableGatewayAPI` restructured to `gatewayAPI.enabled` / `gatewayAPI.enableListenerSet` (old fields deprecated but still work).
- **cainjector**: `CAInjectorMerging` promoted to GA; `--ignore-namespaces` flag; unconditional server-side apply.
- ⚠️ **Breaking**: default `tokenrequest` RBAC (`Role`/`RoleBinding` granting `serviceaccounts/token: create` on the controller ServiceAccount) removed from the Helm chart — create your own or use a dedicated ServiceAccount.
- ⚠️ **Possibly breaking**: `cert-manager-edit` no longer grants `create` for `challenges` or `create`/`patch`/`update` for `orders` (GHSA-8rvj-mm4h-c258; already present in v1.20.3/v1.19.6).
- ⚠️ **Breaking**: Helm values `prometheus.servicemonitor.targetPort`, `prometheus.servicemonitor.path`, `prometheus.podmonitor.path` removed; metrics port renamed `tcp-prometheus-servicemonitor` → `http-metrics` — residual keys fail schema validation on upgrade.
- ⚠️ **Known issues**: controller crash-loops on any Certificate with `renewal.policy: Disabled`, cosmetic Secret-event log spam, and Issuers can stick at `InvalidSolver` — all fixed in v1.21.1; run v1.21.1 instead.

## v1.20.4 — 2026-09-16

[Release page](https://github.com/jetstack/cert-manager/releases/tag/v1.20.4) — all users should upgrade.

- Security: Go 1.26.5/1.26.6 (go command and stdlib `net` packages), x/net 0.58.0, x/text 0.41.0, x/crypto 0.55.0 (CVE-2026-46600, CVE-2026-56852, CVE-2026-56854), grpc 1.83.2 (CVE-2026-84303, CVE-2026-84304, CVE-2026-84445, GHSA-hrxh-6v49-42gf), cel-go 0.30.0, go-pkcs12 0.7.2.
- Fixes the ingress-shim removing the `applyset` label from cached Ingress and Gateway objects.
- Note: three `golang.org/x/crypto` scanner findings remain but do not affect cert-manager (SSH connection-multiplexer deadlocks — cert-manager never opens an SSH connection — and the unmaintained `openpgp` package — not imported); the 1.21 line already uses x/crypto 0.56.0, so upgrade to 1.21 for a clean scan.
- Release staging now signs `metadata.json` with cosign so the publish step can verify its authenticity.

## v1.20.3 — 2026-06-25

[Release page](https://github.com/jetstack/cert-manager/releases/tag/v1.20.3) — all users should upgrade.

- **Security fix (HIGH, [GHSA-8rvj-mm4h-c258](https://github.com/cert-manager/cert-manager/security/advisories/GHSA-8rvj-mm4h-c258))**: the `cert-manager-edit` aggregate ClusterRole no longer lets namespace users create ACME `Challenge`/`Order` resources directly, which previously allowed attacker-controlled solver config while credentials loaded from the `ClusterIssuer`'s namespace (could disclose DNS credentials, notably for the acme-dns provider).
- ⚠️ **Possibly breaking**: same RBAC restriction — tooling that creates Challenges/Orders directly needs explicit permissions.
- Removes the issuer owner reference from Challenges that was blocking Challenge garbage collection; Go 1.26.3/1.26.4 and dependency CVE fixes.

## v1.20.2 — 2026-04-11

[Release page](https://github.com/jetstack/cert-manager/releases/tag/v1.20.2)

- Helm: fixes invalid YAML generated when both `webhook.config` and `webhook.volumes` are defined.
- Go 1.26.2 + dependency bumps for reported vulnerabilities.

## v1.20.1 — 2026-03-27

[Release page](https://github.com/jetstack/cert-manager/releases/tag/v1.20.1)

- Adds the missing issuer finalizer RBAC to the order controller — this was preventing OpenShift users from upgrading to v1.20.0.
- Fixes a duplicate `parentRef` bug (Gateway API) when both issuer config and annotations are present.
- grpc bump for a scanner-reported (non-affecting) vulnerability.

## v1.20.0 — 2026-03-10

[Release page](https://github.com/jetstack/cert-manager/releases/tag/v1.20.0)

- **ListenerSet** support (alpha, `XListenerSets` feature gate) and **Azure Private DNS Zones** for DNS-01.
- Gateway API: `parentRefs` no longer required for ACME; `parentRef` override annotations on `Certificate`; new `acme.cert-manager.io/http01-ingress-ingressclassname` Ingress annotation.
- OtherNames promoted to Beta; feature flags to configure NetworkPolicies across all containers; `extraContainers` Helm value for sidecars (e.g. AWS IAM Roles Anywhere); `imagePullSecrets` for the startupapicheck job; PDB `unhealthyPodEvictionPolicy`; configurable PEM decoding size limits; selectable CRD fields for `spec.issuerRef.{group,kind,name}`).
- Vault issuers include the Vault server address as a default audience on generated service-account tokens.

## v1.19.6 — 2026-06-25

[Release page](https://github.com/jetstack/cert-manager/releases/tag/v1.19.6) — all users should upgrade.

- Same security fix as v1.20.3 ([GHSA-8rvj-mm4h-c258](https://github.com/cert-manager/cert-manager/security/advisories/GHSA-8rvj-mm4h-c258), HIGH) backported to the 1.19 line.
- ⚠️ **Possibly breaking**: `cert-manager-edit` RBAC restriction (no direct `Challenge`/`Order` writes).
- Go 1.25.x and dependency CVE fixes.

## v1.19.5 — 2026-04-21

[Release page](https://github.com/jetstack/cert-manager/releases/tag/v1.19.5)

- Patch release for reported vulnerabilities: Go 1.25.8/1.25.9 and dependency bumps. Upgrade recommended.
