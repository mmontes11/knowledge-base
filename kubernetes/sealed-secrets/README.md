---
upstream: https://github.com/bitnami-labs/sealed-secrets
last_updated: 2026-08-18
---

# sealed-secrets

[Sealed Secrets](https://github.com/bitnami/sealed-secrets#sealed-secrets) solves the last gap in "everything in git": a regular Kubernetes `Secret` is sealed (encrypted) with a cluster controller's RSA public key into a `SealedSecret` custom resource, which is safe to store in a public repository. Only the sealed-secrets controller running in the target cluster holds the private key; when it sees a `SealedSecret`, it unseals it and creates/updates a plain `Secret` with the same name.

- Upstream repository: https://github.com/bitnami-labs/sealed-secrets — moved to the [bitnami/sealed-secrets](https://github.com/bitnami/sealed-secrets) org in v0.38.0; the old URL redirects.
- Documentation: [README on GitHub](https://github.com/bitnami/sealed-secrets#readme) is canonical; the `site/` Hugo docs tree in the repo redirects to it.
- License: Apache-2.0
- API group/version: `sealedsecrets.bitnami.com/v1alpha1` (kind `SealedSecret`)

## Standard documents

- [API Reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
