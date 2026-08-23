---
upstream: https://github.com/vllm-project/vllm
last_updated: 2026-08-23
---

# vllm — project overview

vLLM is a high-throughput, memory-efficient inference and serving engine for LLMs. Originally developed in the [Sky Computing Lab](https://sky.cs.berkeley.edu) at UC Berkeley (paper: [Efficient Memory Management for Large Language Model Serving with PagedAttention](https://arxiv.org/abs/2309.06180)), it serves 200+ Hugging Face model architectures via continuous batching, [PagedAttention](https://blog.vllm.ai/2023/06/20/vllm.html) KV-cache management, quantization (FP8, MXFP4/MXFP8, NVFP4, INT8/INT4, GPTQ/AWQ, GGUF, and more), and tensor/pipeline/data/expert/context parallelism across NVIDIA, AMD, and Intel GPUs plus CPUs and various accelerator plugins ([README](https://github.com/vllm-project/vllm#about)).

## Facts

- **Upstream repository**: [vllm-project/vllm](https://github.com/vllm-project/vllm) (default branch `main`; ~90k stars, 8,000+ open items at time of writing — one of the most active OSS AI projects)
- **Documentation**: <https://docs.vllm.ai>
- **Website**: <https://vllm.ai> · **Blog**: <https://blog.vllm.ai>
- **License**: Apache-2.0 ([LICENSE](https://github.com/vllm-project/vllm/blob/main/LICENSE))
- **Language**: Python (with C++/CUDA kernels)
- **Distribution**: PyPI `vllm` (via `uv pip install vllm` / `pip install vllm`), Docker images (e.g. `vllm/vllm-openai`)
- **Companion stack**: the [vllm-project/production-stack](https://github.com/vllm-project/production-stack) reference Helm stack (serving engine + request router + Prometheus/Grafana observability) — docs at <https://docs.vllm.ai/projects/production-stack>

## Usage in this stack

Deployed in [mmontes11/k8s-ai](https://github.com/mmontes11/k8s-ai) as Flux GitOps under `infrastructure/vllm/`

- `HelmRelease`/`HelmRepository` pair: chart **`vllm-stack`** from `https://vllm-project.github.io/production-stack` (pinned `0.1.10`).
- Serving engine: image `vllm/vllm-openai:cu130-nightly`, model `Sehyo/Qwen3.5-35B-A3B-NVFP4` pre-fetched into a `topolvm` PVC by an init container, `nvidia` runtime class, 1 GPU + `nvidia.com/gpu.present` node affinity; prefix caching, chunked prefill, FP8 KV cache, reasoning parser `qwen3`, and `--enable-sleep-mode` enabled; Hugging Face access token via the `hf-token` SealedSecret (referenced by name only).
- Request router: `lmcache/lmstack-router` (pinned `v0.1.10`), exposed through an internal HTTPRoute on the Traefik gateway.
- Observability: ServiceMonitors on engine and router.
- **Currently DISABLED**: the `vllm` Kustomization is commented out in `clusters/homelab/infrastructure.yaml` with the note "CUDA Out of Memory when deploying Qwen3.5-35B-A3B-NVFP4" (verified 2026-08-18).

## Standard documents

- [api-reference.md](api-reference.md) — API surface (HTTP endpoints, Python API, CLI)
- [releases.md](releases.md) — latest 10 releases
- [features.md](features.md) — feature documentation
