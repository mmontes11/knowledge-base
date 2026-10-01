---
upstream: https://github.com/kserve/kserve
last_updated: 2026-10-01
---

# kserve — features

Significant feature areas with links to the relevant upstream docs or the release that
introduced them. The [documentation site](https://kserve.github.io/website/) (source:
[kserve/website](https://github.com/kserve/website)) is authoritative; the per-Kind
[CRD reference](https://kserve.github.io/website/docs/reference/crd-api) is the API surface.

## InferenceService and deployment modes

The `InferenceService` is the single entry point: it declares the predictor (plus optional
transformer and explainer), the model source (object storage / Git / OCI), and the **deployment
mode** — *standard* (Knative serverless, scale-to-zero, canary) or *raw* (plain `Deployment`).

- [InferenceService CRD](https://github.com/kserve/kserve/blob/master/config/crd/full/serving.kserve.io_inferenceservices.yaml)
- [Quickstart](https://kserve.github.io/website/docs/next/getting-started/quickstart-guide)

## Serving runtimes

A *serving runtime* is a container that serves one ML framework / accelerator combination
(TensorFlow, PyTorch, ONNX, XGBoost, scikit-learn, Hugging Face, Triton, Seldon, AutoGluon,
…). KServe selects a runtime from the model's `modelFormat`; vLLM was promoted to a first-class
runtime in v0.20.0.

- [vLLM runtime (v0.20.0)](https://github.com/kserve/kserve/releases/tag/v0.20.0)
- [Documentation site](https://kserve.github.io/website/)

## LLM inference (LLMInferenceService)

`LLMInferenceService` is a Gateway-API-based workload that provisions an LLM **inference pool**
(vLLM engine + llm-d router/scheduler) behind an `HTTPRoute`, with `LLMInferenceServiceConfig`
providing reusable scaling / storage / engine settings. Introduced in v0.16.0; since then it has
gained WVA/KEDA autoscaling (v0.18.0), `LocalModelCache` support (v0.19.0), Managed DRA, CPU
KV-cache offloading, and the Anthropic Messages API (v0.20.0). v0.21.0 adds direct KEDA
scale-to-zero (`idleReplicaCount=0`), `rolloutStrategy`, `LocalModelCache` support for LoRA
adapters, and moves WVA autoscaling to annotation-based discovery (WVA >= v0.8.0).

- [LLMInferenceService CRD](https://github.com/kserve/kserve/blob/master/config/crd/full/llmisvc/serving.kserve.io_llminferenceservices.yaml)
- [API introduced (v0.16.0)](https://github.com/kserve/kserve/releases/tag/v0.16.0)

## Local model cache

`LocalModelCache` (plus `LocalModelNode` / `LocalModelNodeGroup`) pre-stages model weights on a
group of nodes so co-located `InferenceService`s don't re-download the same weights. A
namespace-scoped variant (`LocalModelNamespaceCache`) was added in v0.18.0 and `LocalModelCache`
support for `LLMInferenceService` in v0.19.0.

- [LocalModelCache CRD](https://github.com/kserve/kserve/blob/master/config/crd/full/localmodel/serving.kserve.io_localmodelcaches.yaml)

## Model Cache Vault (kernelcache)

The Model Cache Vault (`kernelcache/mcv`, added in v0.21.0) is a WASM-inspired container
packaging utility for Model/GPU kernel caches: it builds single-layer container images
containing GPU kernel/model caches, extracts caches from OCI images, and signs artifacts and
images with cosign (including cert-mode signature verification).

- [Model Cache Vault](https://github.com/kserve/kserve/tree/master/kernelcache/mcv)
- [Initial code donation (v0.21.0)](https://github.com/kserve/kserve/pull/5590)

## InferenceGraph

`InferenceGraph` composes multiple `InferenceService`s into a directed graph of steps —
sequential, conditional, or parallel, with retries — for multi-model pipelines.

- [InferenceGraph CRD](https://github.com/kserve/kserve/blob/master/config/crd/full/serving.kserve.io_inferencegraphs.yaml)

## Multi-node / multi-GPU serving

An `InferenceService` can shard a model across multiple nodes / GPUs (vLLM + Ray), added in
v0.15.0 and extended to OCI storage and `LWS` (LeaderWorkerSet) backends in v0.16.0.

- [Multi-node (v0.15.0)](https://github.com/kserve/kserve/releases/tag/v0.15.0)

## Storage and model sources

Models are pulled by a storage-initializer init container from object storage (S3 / GCS /
Azure), Git, PVC, and — since v0.20 — multiple OCI sources in `storageUris`. v0.21.0 adds
KServe-side `oci+fetch://` image pull and ModelScope (`ms://`) downloads.
`ClusterStorageContainer` provides named, reusable storage defaults.

- [ClusterStorageContainer CRD](https://github.com/kserve/kserve/blob/master/config/crd/full/clusterstoragecontainer/serving.kserve.io_clusterstoragecontainers.yaml)
- Multiple OCI sources (v0.20.0)

## Model lifecycle and explainability

Stop/resume for models, transformers, explainers, and inference graphs (v0.15/v0.16);
`TrainedModel` for loading/unloading a named model on an existing service; what-if explainers and
inference logging (optionally to blob storage). Since v0.21.0, `InferenceService` also accepts an
OpenTelemetry `spec.tracing` configuration.

- [Stop/resume (v0.16.0)](https://github.com/kserve/kserve/releases/tag/v0.16.0)
- [TrainedModel CRD](https://github.com/kserve/kserve/blob/master/config/crd/full/serving.kserve.io_trainedmodels.yaml)
