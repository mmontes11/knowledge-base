---
upstream: https://github.com/bitnami-labs/sealed-secrets
last_updated: 2026-08-18
---

# sealed-secrets — API Reference

Sealed Secrets registers exactly one custom resource kind, `SealedSecret`, under the API group `sealedsecrets.bitnami.com`, version **v1alpha1** (the only served/stored version). It is a namespaced resource (plural `sealedsecrets`). The canonical field schema is checked in at [schema-v1alpha1.yaml](https://github.com/bitnami/sealed-secrets/blob/main/schema-v1alpha1.yaml) and ships with the Helm chart under [`helm/sealed-secrets/crds/`](https://github.com/bitnami/sealed-secrets/tree/main/helm/sealed-secrets/crds).

| Kind | API Version | Purpose | Upstream API Docs |
| ---- | ----------- | ------- | ----------------- |
| `SealedSecret` | `sealedsecrets.bitnami.com/v1alpha1` | Sealed (RSA-encrypted) representation of a Kubernetes `Secret`; the controller unseals it and maintains a matching plain `Secret`. | [schema](https://github.com/bitnami/sealed-secrets/blob/main/schema-v1alpha1.yaml) |

## `SealedSecretSpec`

- `encryptedData` (required): `map[string]string` — one entry per key of the target `Secret`, each value the base64-encoded sealed payload for that single value. This is the per-value format `kubeseal` emits today.
- `data` (deprecated, `format: byte`): legacy whole-`Secret` payload sealed as one base64 string. Superseded by per-key `encryptedData`; use per-key sealing for new work.
- `template` (optional): declares the `Secret` the controller materializes:
  - `metadata`: name/namespace/labels/annotations/finalizers of the created `Secret` (`x-kubernetes-preserve-unknown-fields` — arbitrary metadata keys are accepted).
  - `data`: `map[string]string` — extra keys whose values are Go-template strings evaluated at unseal-time against the decrypted values, allowing derived keys (plaintext `template.data` became visible in the template context in v0.37.0, [#1940](https://github.com/bitnami/sealed-secrets/pull/1940)).
  - `immutable`: mark the created `Secret` immutable (only metadata then changeable).
  - `type`: `Secret` type, e.g. `Opaque`, `kubernetes.io/tls`.

## `SealedSecretStatus`

- `observedGeneration` (int64): generation last observed by the controller.
- `conditions`: array of `SealedSecretCondition` — documented type is `Synced`, with `status` `True`/`False`/`Unknown`, `reason`, `message`, `lastUpdateTime`, `lastTransitionTime`; `type` and `status` are required.

Notes

- The generated `Secret` keeps the `SealedSecret`'s name and, by default, its namespace.
- Where a sealed payload may be unsealed — `strict` (default), `namespace-wide`, `cluster-wide` — is fixed at seal time: `strict` bakes the original name and namespace into the ciphertext, while the other scopes are recorded via the `kubeseal --scope` flag / the `sealedsecrets.bitnami.com/namespace-wide` and `sealedsecrets.bitnami.com/cluster-wide` annotations.
- Non-CRD surfaces are the chart (`helm/sealed-secrets/values.yaml`: `--key-secret`, `--additional-namespace`, Prometheus `ServiceMonitor`/`PrometheusRule`/Grafana dashboard toggles) and the controller's admin HTTP endpoints — see [Features](features.md).
