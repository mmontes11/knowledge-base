---
upstream: https://github.com/kserve/kserve
last_updated: 2026-08-22
---

# kserve

KServe is a CNCF-incubating, Kubernetes-native platform for the deployment and scaling of
traditional machine-learning models and large language models (LLMs). It presents a single
`InferenceService` API that covers both **standard** (Knative-based serverless, scale-to-zero,
canary) and **raw** (`Deployment`) deployment modes, an ecosystem of framework- and
accelerator-specific *serving runtimes*, `InferenceGraph` composition of multiple services, and a
Gateway-API-based `LLMInferenceService` workload for LLM inference pooling (vLLM + llm-d
routing/scheduling). Models are loaded from object storage, Git, or OCI registries, or
pre-provisioned on nodes via a local model cache.

- Upstream repository: [kserve/kserve](https://github.com/kserve/kserve)
- Documentation: [kserve.github.io/website](https://kserve.github.io/website/) (source: [kserve/website](https://github.com/kserve/website))
- License: Apache-2.0 ([LICENSE](https://github.com/kserve/kserve/blob/master/LICENSE))
- API group: `serving.kserve.io` — `InferenceService` is served/stored at `v1beta1`; all supporting Kinds are at `v1alpha1`, with `LLMInferenceService`/`LLMInferenceServiceConfig` additionally served at `v1alpha2` (via conversion webhook)
- Helm charts (published to `oci://ghcr.io/kserve/charts/`): `kserve-crd`, `kserve-resources`, `kserve-runtime-configs`, `kserve-llmisvc-crd`, `kserve-llmisvc-resources`

## Usage in this stack

Our infrastructure repo deploys KServe via Kustomizations under `infrastructure/kserve`:
`kserve-crds` installs the API types, `kserve-resources` deploys the controllers and serving-runtime
configs, and `kserve-models` deploys the model workloads. All charts are pulled from
`oci://ghcr.io/kserve/charts/`.

## Standard docs

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
