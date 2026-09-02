---
upstream: https://github.com/kserve/kserve
last_updated: 2026-08-22
---

# kserve — API reference

KServe registers twelve custom resource Kinds under the API group **`serving.kserve.io`**.
`InferenceService` is served and stored at **`v1beta1`**; every other Kind is at **`v1alpha1`**,
and `LLMInferenceService` / `LLMInferenceServiceConfig` are additionally served at
**`v1alpha2`** (with an automatic conversion webhook). The `Cluster*` Kinds
(`ClusterServingRuntime`, `ClusterStorageContainer`) are cluster-scoped; the rest are namespaced.
Go types live under [`pkg/apis/`](https://github.com/kserve/kserve/tree/master/pkg/apis) and the CRD
manifests under [`config/crd/full/`](https://github.com/kserve/kserve/tree/master/config/crd/full).

The human-readable API docs on the [CRD reference](https://kserve.github.io/website/docs/reference/crd-api)
are authoritative; the per-Kind CRD manifests linked below are the source of truth for the schema.

| Kind | Version | Purpose | Upstream CRD |
| ---- | ------- | ------- | ------------ |
| `InferenceService` | `v1beta1` | Root resource: declares predictor / transformer / explainer components, model sources, and the deployment mode (standard or raw). | [CRD](https://github.com/kserve/kserve/blob/master/config/crd/full/serving.kserve.io_inferenceservices.yaml) |
| `ServingRuntime` | `v1alpha1` | Namespaced serving-runtime definition (framework + container) used to build predictors. | [CRD](https://github.com/kserve/kserve/blob/master/config/crd/full/serving.kserve.io_servingruntimes.yaml) |
| `ClusterServingRuntime` | `v1alpha1` | Cluster-scoped variant of `ServingRuntime`, applied to all namespaces. | [CRD](https://github.com/kserve/kserve/blob/master/config/crd/full/serving.kserve.io_clusterservingruntimes.yaml) |
| `ClusterStorageContainer` | `v1alpha1` | Cluster-scoped storage (volume) config referenced by name for model loading. | [CRD](https://github.com/kserve/kserve/blob/master/config/crd/full/clusterstoragecontainer/serving.kserve.io_clusterstoragecontainers.yaml) |
| `InferenceGraph` | `v1alpha1` | Directed graph of inference steps (sequential / conditional / parallel, with retries) composing multiple `InferenceService`s. | [CRD](https://github.com/kserve/kserve/blob/master/config/crd/full/serving.kserve.io_inferencegraphs.yaml) |
| `TrainedModel` | `v1alpha1` | Lightweight resource that loads/unloads a named model on an existing `InferenceService`. | [CRD](https://github.com/kserve/kserve/blob/master/config/crd/full/serving.kserve.io_trainedmodels.yaml) |
| `LocalModelCache` | `v1alpha1` | Defines a local model cache so co-located `InferenceService`s share already-downloaded model weights on a node group. | [CRD](https://github.com/kserve/kserve/blob/master/config/crd/full/localmodel/serving.kserve.io_localmodelcaches.yaml) |
| `LocalModelNamespaceCache` | `v1alpha1` | Namespace-scoped model cache (added in v0.18.0). | [CRD](https://github.com/kserve/kserve/blob/master/config/crd/full/localmodel/serving.kserve.io_localmodelnamespacecaches.yaml) |
| `LocalModelNode` | `v1alpha1` | Represents a node participating in a local model cache (added in v0.15.0). | [CRD](https://github.com/kserve/kserve/blob/master/config/crd/full/localmodel/serving.kserve.io_localmodelnodes.yaml) |
| `LocalModelNodeGroup` | `v1alpha1` | Named set of nodes targeted by a model cache. | [CRD](https://github.com/kserve/kserve/blob/master/config/crd/full/localmodel/serving.kserve.io_localmodelnodegroups.yaml) |
| `LLMInferenceService` | `v1alpha1` / `v1alpha2` | Declarative LLM workload that provisions an inference pool (vLLM + llm-d router/scheduler) behind a Gateway-API `HTTPRoute`. | [CRD](https://github.com/kserve/kserve/blob/master/config/crd/full/llmisvc/serving.kserve.io_llminferenceservices.yaml) |
| `LLMInferenceServiceConfig` | `v1alpha1` / `v1alpha2` | Named, reusable configuration (scaling, storage, engine options) for `LLMInferenceService`. | [CRD](https://github.com/kserve/kserve/blob/master/config/crd/full/llmisvc/serving.kserve.io_llminferenceserviceconfigs.yaml) |

Notes:

- A **minimal** CRD set (only `InferenceService`) is published under
  [`config/crd/minimal/`](https://github.com/kserve/kserve/tree/master/config/crd/minimal) for lighter
  installs; the `kserve-crd` Helm chart ships the full set.
- `LLMInferenceService` / `LLMInferenceServiceConfig` are installed by the separate
  `kserve-llmisvc-crd` / `kserve-llmisvc-resources` charts and have a dedicated conversion webhook.
- Field-level details are intentionally not duplicated here; follow the per-Kind link or the
  [CRD reference](https://kserve.github.io/website/docs/reference/crd-api).
