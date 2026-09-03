---
upstream: https://github.com/vllm-project/vllm
last_updated: 2026-09-03
---

# vllm — features

Feature areas with links to the upstream documentation.

## KV cache management (PagedAttention)

The core performance mechanism: attention KV state is allocated in fixed-size blocks that are reference-counted and shared across sequences, eliminating most KV-cache fragmentation and enabling zero-copy sharing for techniques like parallel sampling. Introduced in the [PagedAttention blog post](https://blog.vllm.ai/2023/06/20/vllm.html) and paper; the implementation details (V1/MRv2 backends) are documented in the [design docs](https://docs.vllm.ai/en/latest/design/index). Note: the legacy *PagedAttention attention kernel* was **removed in v0.25.0** in favor of the V1/Model Runner V2 attention backends.

## Scheduling: continuous batching, chunked prefill, prefix caching

Requests are scheduled per-iteration (continuous batching); long prefills are chunked so decode latency stays flat; and shared prompt prefixes are cached and reused across requests. See [automatic prefix caching](https://docs.vllm.ai/en/latest/features/automatic_prefix_caching).

## Model support

200+ Hugging Face architectures: decoder-only LLMs, MoE (DeepSeek-V3/Qwen-MoE/GPT-OSS), hybrid attention + state-space models (Mamba, Qwen3.5), multimodal, embedding/retrieval, and reward/classification models. Full list: [supported models](https://docs.vllm.ai/en/latest/models/supported_models); model-usage recipes at [recipes.vllm.ai](https://recipes.vllm.ai).

## Multimodal inputs

Images, video, and audio inputs across chat/completions/embeddings paths, with pluggable processors (incl. a Transformers-modeling-backend path and PyNvVideoCodec hardware decoding). See [multimodal inputs](https://docs.vllm.ai/en/latest/features/multimodal_inputs) and [speech-to-text serving](https://docs.vllm.ai/en/latest/serving/online_serving/speech_to_text).

## Quantization

Weight, activation, and KV-cache quantization for FP8, MXFP8/MXFP4, NVFP4, INT8/INT4, GPTQ/AWQ, GGUF, compressed-tensors, ModelOpt, TorchAO, and more — with per-hardware kernel backends (CUTLASS, TRTLLM-GEN, CuTeDSL, quark/AITER on ROCm). See [quantization](https://docs.vllm.ai/en/latest/features/quantization) (per-backend pages underneath).

## Speculative decoding

Draft-then-verify decoding with n-gram, suffix, EAGLE/EAGLE-3, MTP, DFlash, DFlash2 (since v0.28.0), and DSpark drafters (including heterogeneous-vocabulary TLI and dynamic speculators); respects reasoning/thinking budgets. See [speculative decoding examples](https://docs.vllm.ai/en/latest/examples/features/speculative_decoding).

## Structured outputs, tool calling, and reasoning parsers

Constrained decoding (JSON grammar, regex, EBNF) for exact-schema generation via xgrammar/guidance; tool-call parsing (incl. the unified Streaming Parser Engine since v0.24.0); and reasoning/thinking token extraction and interleaved-thinking control. See [structured outputs](https://docs.vllm.ai/en/latest/features/structured_outputs), [tool calling](https://docs.vllm.ai/en/latest/features/tool_calling), and [reasoning outputs](https://docs.vllm.ai/en/latest/features/reasoning_outputs).

## Multi-LoRA

Serving many LoRA adapters alongside the base dense or MoE model in one engine, with per-request adapter selection and dynamic adapter loading/unloading on the server. See [LoRA](https://docs.vllm.ai/en/latest/features/lora).

## Distributed inference (parallelism)

Tensor, pipeline, data, expert, and context parallelism for scaling to multi-GPU and multi-node clusters, including data-parallel deployments via a Rust DP supervisor and Ray-based elastic expert parallelism. See [parallelism & scaling](https://docs.vllm.ai/en/latest/serving/parallelism_scaling), [data-parallel deployment](https://docs.vllm.ai/en/latest/serving/data_parallel_deployment), [expert-parallel deployment](https://docs.vllm.ai/en/latest/serving/expert_parallel_deployment), and [context-parallel deployment](https://docs.vllm.ai/en/latest/serving/context_parallel_deployment).

## Disaggregated serving and KV cache offloading

Prefill/decode (and encoder) disaggregation across instances via pluggable KV-transfer connectors (NIXL, Mooncake, MoRIIO), plus tiered KV cache offloading to CPU/RAM/object stores/disk for cache reuse beyond GPU memory. See [disaggregated prefill](https://docs.vllm.ai/en/latest/features/disagg_prefill), [KV offloading usage](https://docs.vllm.ai/en/latest/features/kv_offloading_usage), [NIXL connector](https://docs.vllm.ai/en/latest/features/nixl_connector_usage), and [disaggregated encoder](https://docs.vllm.ai/en/latest/features/disagg_encoder).

## Sleep mode

`--enable-sleep-mode` lets engines release GPU memory while idle and wake on demand (weight reload supported since v0.22.0) — used by the homelab deployment to coexist with other GPU workloads. See [sleep mode](https://docs.vllm.ai/en/latest/features/sleep_mode).

## Hardware support

NVIDIA, AMD (ROCm), and Intel GPUs; x86/ARM/PowerPC CPUs; plus hardware plugins for Google TPU, Intel Gaudi, IBM Spyre, Huawei Ascend, and others (Rubin `sm_107` and ROCm gfx1250 enabled from v0.27.0). See [installation by backend](https://docs.vllm.ai/en/latest/getting_started/installation) and [platforms API](https://docs.vllm.ai/en/latest/api/vllm/platforms/).

## API compatibility

Drop-in OpenAI-compatible server (also Anthropic Messages API and gRPC), so existing OpenAI SDK clients work unmodified. See [OpenAI-Compatible Server](https://docs.vllm.ai/en/latest/serving/online_serving/openai_compatible_server).

## Deterministic / batch-invariant inference

Batch invariance guarantees identical results regardless of batching (useful for RL rollouts), with Cutlass FP8 and NVFP4 paths (28.9% E2E latency win since v0.22.0). See [batch invariance](https://docs.vllm.ai/en/latest/features/batch_invariance).

## Observability

Prometheus metrics on `/metrics`, optional per-request metrics, structured logging configuration, and optional OpenTelemetry tracing; the [production-stack](https://github.com/vllm-project/production-stack) Helm chart ships a Prometheus + Grafana observability stack. See [per-request metrics](https://docs.vllm.ai/en/latest/features/per_request_metrics).

## Rust frontend (new)

An experimental Rust front-end (in-tree since v0.22.0) provides a second, performance-focused API server implementation with a gRPC control plane, streaming `generate`, and a native `vllm-bench`; it can take over selected `vllm` CLI subcommands. Tracked through release notes; see the [grpc_server API](https://docs.vllm.ai/en/latest/api/vllm/entrypoints/grpc_server).
