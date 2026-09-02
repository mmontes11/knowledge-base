---
upstream: https://github.com/kserve/kserve
last_updated: 2026-08-22
---

# kserve — releases

Latest 10 official releases, newest first. Check the ⚠️ entries before upgrading.

## v0.20.0 — 2026-08-06

[Release page](https://github.com/kserve/kserve/releases/tag/v0.20.0)

- vLLM runtime support and AutoGluon Server; confidential model serving.
- `LLMInferenceService`: Managed DRA support, CPU KV-cache tiering (`kvCacheOffloading`), Anthropic Messages API (`v1/messages` `HTTPRoute`), LLM inference tracing, `LLMInferenceServiceConfig` finalizer.
- Dependency upgrades: Gateway API v1.5.1, GIE v1.5.0 (with a local `v1alpha2` `InferencePool` shim), llm-d v0.8.0.

## v0.18.1 — 2026-07-15

[Release page](https://github.com/kserve/kserve/releases/tag/v0.18.1)

- Patch release: cherrypicked helm chart fix.

## v0.19.0 — 2026-06-14

[Release page](https://github.com/kserve/kserve/releases/tag/v0.19.0)

- `LocalModelCache` support for `LLMInferenceService`; dual-protocol (REST/gRPC) routing for standard-mode predictors.
- Upgrade path to llm-d v0.6 components; Envoy AI Gateway v0.6.0 / Envoy Gateway v1.7.0.
- `InferenceService`: fixed OCI support for `storageUris` (plural); reported the observed routing topology and workload references in status.

## v0.17.1 — 2026-05-21

[Release page](https://github.com/kserve/kserve/releases/tag/v0.17.1)

- Patch release: cherrypicked helm chart fix; install scripts from 0.17.0 included.

## v0.18.0 — 2026-04-29

[Release page](https://github.com/kserve/kserve/releases/tag/v0.18.0)

- New **namespace-scoped** model cache (`LocalModelNamespaceCache`).
- ⚠️ main `kserve` Helm chart renamed to `kserve-resources` (the chart set is now `kserve-crd`, `kserve-resources`, `kserve-runtime-configs`, `kserve-llmisvc-crd`, `kserve-llmisvc-resources`).
- `LLMInferenceService`: autoscaling via KEDA/HPA (`WVA`) and storage migrations; GIE CRDs shipped in the KServe bundle; new CSV and Parquet marshallers; llm-d 0.6.

## v0.17.0 — 2026-03-13

[Release page](https://github.com/kserve/kserve/releases/tag/v0.17.0)

- ⚠️ **BREAKING**: Helm charts reorganized into `kserve-crd` / `kserve-resources` / `kserve-runtime-configs` / `kserve-llmisvc-*`; see the [upgrade guide](https://kserve.github.io/website/docs/install/upgrade-guide#upgrading-v0160-to-v0170).
- `pathTemplate` support for `InferenceService` routing; Go 1.25 and kubebuilder 1.9.0.
- Bumped Gateway API Inference Extension to v1.2.0; separated the `LocalModelCache` webhook from the main controller.

## v0.16.0 — 2025-11-03

[Release page](https://github.com/kserve/kserve/releases/tag/v0.16.0)

- ⚠️ New **`LLMInferenceService`** / `LLMInferenceServiceConfig` API types + CRDs and initial controller scaffold (the Gateway-based LLM path).
- Stop/resume for models, transformers, explainers, and inference graphs (serverless + raw).
- vLLM v0.9.x / Torch 2.6–2.7; OpenTelemetry Collector autoscaling on multiple metrics; dropped pydantic v1; switch to `uv`.

## v0.15.2 — 2025-05-27

[Release page](https://github.com/kserve/kserve/releases/tag/v0.15.2)

- Fixed CVE-2025-43859; enabled ModelCar by default; reworked Knative autoscaler configmap sourcing during reconcile.

## v0.15.1 — 2025-05-15

[Release page](https://github.com/kserve/kserve/releases/tag/v0.15.1)

- vLLM V1 support + LMCache integration in the Hugging Face serving runtime; NumPy 2.x; bumped Go to 1.24.
- Model cache no longer deletes PVC/PV after `InferenceService` deletion; stop/resume of models (serverless) via annotation; vLLM upgrades for Llama 4 and Qwen 3.

## v0.15.0 — 2025-03-31

[Release page](https://github.com/kserve/kserve/releases/tag/v0.15.0)

- **Multi-node inference** implementation (vLLM / Ray); `LocalModelNode` CR; vLLM tools (function calling) support.
- HF-transfer download acceleration; GCS single-file downloads; bumped vLLM to 0.6.3.
