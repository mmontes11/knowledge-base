---
upstream: https://github.com/vllm-project/vllm
last_updated: 2026-08-23
---

# vllm — releases

Latest 10 official releases, newest first. Check the ⚠️ entries before upgrading. Release cadence is roughly biweekly majors plus occasional patches.

## v0.27.1 — 2026-08-11

[Release page](https://github.com/vllm-project/vllm/releases/tag/v0.27.1)

- Patch release on v0.27.0: support for quantized DSpark Markov heads (#50424).

## v0.27.0 — 2026-08-10

[Release page](https://github.com/vllm-project/vllm/releases/tag/v0.27.0)

- **Kimi K3** lands as a full stack: model files/kernels, Python and Rust frontends, AttnRes kernels, DeepGEMM, compressed-tensors checkpoints (#50089, #50093, #50104, #50458, #50500).
- ⚠️ **PyTorch 2.13.0 upgrade** (torchvision 0.28.0, Triton 3.7.1) — a breaking environment change, also for XPU and CPU (#48155).
- ⚠️ **Removals**: models Plamo2 and Ouro removed; the unsupported `max_num_partial_prefills` / `max_long_partial_prefills` arguments are gone (#49729, #49786, #49244).
- New models: Qwen3.5 text-only dense/MoE with EVS video token pruning, K-EXAONE-2.0-750B-A37B, VaultGemma, jina-embeddings-v5-text-nano (#50210, #50524, #49803, #50688).
- FlashAttention 4 deepens on SM100 (FP8 KV cache, headdim-256) with new JIT warmup infrastructure that removes first-request compilation stalls (#42569, #42669, #47451).
- Model Runner V2 expands to non-generative workloads (encoder-only, pooling, token classification/embedding); DeepSeek-V4 gets sequence parallelism and multiple E2E TTFT wins; simplified fault-tolerance framework for DP+EP external load-balancer deployments (#44428).
- Rust frontend grows a gRPC control plane (engine health, abort control, server/model discovery) and `vllm-bench` joins the `vllm` CLI; early next-gen hardware enablement: `sm_107` (NVIDIA Rubin) and ROCm gfx1250 (#48992, #49387, #46516).

## v0.26.0 — 2026-07-27

[Release page](https://github.com/vllm-project/vllm/releases/tag/v0.26.0)

- ⚠️ **Models removed**: TeleChat, Persimmon, Fuyu (#47989, #48096).
- **New Inkling model family** with a full support stack: modeling, piecewise CUDA graphs, Hopper FA4 relative attention, MTP=1 speculative decoding, LoRA, ModelOpt NVFP4 (#48799, #48858, #48869, #48884, #48990).
- KV cache offloading and tiered secondary storage mature substantially: offloading metrics, object-store secondary tier, DP-replica-aware tiering, encoder-cache connectors (#47063, #47987, #47423).
- Attention backends can now be selected per KV-cache group, and sliding-window support is an explicit backend capability (better hybrid-model support) (#48012, #48011).
- fp32 `lm_head` for generation models via `head_dtype` (#48390); security hardening: diskcache removed to eliminate pickle deserialization, derender endpoint bounds, CVE race fix (#44549, #47260, #48583).

## v0.25.1 — 2026-07-14

[Release page](https://github.com/vllm-project/vllm/releases/tag/v0.25.1)

- Patch release: no longer blocks model launch when system FFmpeg is missing (TorchCodec error deferred to runtime); guard for mixed-dtype allreduce+RMSNorm+quant fusion that could corrupt hidden states (#47888, #48330).

## v0.25.0 — 2026-07-11

[Release page](https://github.com/vllm-project/vllm/releases/tag/v0.25.0)

- **Model Runner V2 is now the default for all dense models** (#44443) — it is the standard execution path, with Mamba prefix caching, EVS, realtime embeddings, and dynamic speculative decoding.
- ⚠️ **PagedAttention (legacy attention implementation) has been removed** now that V1/MRv2 backends are the standard path (#47361).
- The Transformers modeling backend reaches native-vLLM performance and gains FP8 MoE (#47187, #46820).
- New models: LLaVA-OneVision-2, Unlimited OCR, MOSS-Transcribe-Diarize, openai/privacy-filter, Hy3; GLM-5 / DeepSeek-V3.2 in the model zoo; MiniMax-M3 pipeline parallelism + NVFP4 (#44785, #47729, #46564, #46808, #45810).
- New **Streaming Parser Engine** unifies tool-call/reasoning parsing (Kimi k2.5/k2.6/k2.7, seed_oss, DeepSeek V4 parsers); universal speculative decoding for heterogeneous vocabularies (TLI) (#46610, #38174).
- Security: decompression-bomb OOM prevention, NaN-audio infinite-loop fix, bounded tokenizer work (#47010, #46463, #47007).

## v0.24.0 — 2026-06-29

[Release page](https://github.com/vllm-project/vllm/releases/tag/v0.24.0)

- **MiniMax-M3 support** with fast follow-ons: BF16/FP8 indexer via MSA, MXFP4, FP8 sparse GQA, broad ROCm tuning (#45381, #45892, #45896, #45744, #45725).
- Model Runner V2 now **supports quantized models by default** and enables GraniteMoE by default; DFlash speculative decoding and FP32 Gumbel sampling accuracy (#44446, #45461, #44586, #45996).
- DeepEP v2 integrated for wide expert parallelism (#41183).
- Rust frontend matures: API-key auth, CORS, `/tokenize`+`/detokenize`, `/pause`/`/resume`/`/is_paused`, `/abort_requests`, `/get_world_size` (#44321, #45753, #44499, #44382, #44801).
- **DiffusionGemma** added incl. CPU path and structured-output guardrails for diffusion decoders (#45163, #45468).
- Security: coordinated hardening batch — audio decompression bomb, speculative-decode DoS, regex-compilation timeout guard, audio size/duration limits (#44970, #44744, #45118, #45510).

## v0.23.0 — 2026-06-15

[Release page](https://github.com/vllm-project/vllm/releases/tag/v0.23.0)

- ⚠️ MiniMax M3 is **not** supported in this version (use v0.24.0+ or the [MiniMax-M3 recipe](https://recipes.vllm.ai/MiniMaxAI/MiniMax-M3)).
- **Targeting Transformers v5**: vLLM now targets Transformers v5, with vendored MiniCPM-V/O processors and compatibility fixes (#44282, #38804, #44559).
- Model Runner V2 is selected by default for Llama and Mistral dense models (in addition to Qwen3), with breakable CUDA graphs and pipeline-parallel bubble elimination (#43458, #44050, #42187).
- Rust frontend growth: streaming `generate` endpoint, dynamic LoRA endpoints, `/version` and `/server_info`, new tool parsers (#43779, #43778, #43854).
- DeepSeek-V4 hardening across backends (decoupled sparse-MLA metadata, TRTLLM-gen kernel, EPLB for Mega-MoE, detach from `torch.compile`); encoder-free Gemma 4 Unified support (#44699, #43827, #44429).

## v0.22.1 — 2026-06-05

[Release page](https://github.com/vllm-project/vllm/releases/tag/v0.22.1)

- Patch release: new model JetBrains **Mellum v2** (open-weights MoE code model); zentorch-accelerated W8A8/W4A16 linear inference on AMD Zen CPUs; fixes for multi-node Ray data-parallel hangs, DeepSeek-V4 initialization, and Olmo/HyperCLOVAX load regressions (#43992, #41813, #43864).

## v0.22.0 — 2026-05-29

[Release page](https://github.com/vllm-project/vllm/releases/tag/v0.22.0)

- **DeepSeek V4** major hardening pass: dedicated `vllm/models/deepseek_v4/` package, NVFP4 fused MoE, full + piecewise CUDA graphs, MTP speculative decoding, MegaMoE fused kernels (#43004, #42209, #42604, #43385).
- Model Runner V2 becomes the default for Qwen3 dense models, with automatic fallback to MRv1 for unsupported features (#39337).
- ⚠️ **Removals**: old `get_tokenizer` / `resolve_hf_chat_template` locations removed; deprecated MLA prefill arguments removed; env vars covered by `--moe-backend` / `--linear-backend` marked deprecated (#35024, #42555, #43148).
- Experimental **Rust frontend** lands (in-tree), with a DP Supervisor for data-parallel serving (#40848, #43283, #40841).
- New multi-tier KV cache offloading framework with a filesystem secondary tier and Mooncake disk offloading; batch-invariant Cutlass FP8 for a 28.9% E2E latency improvement (#40020, #40408).

## v0.21.0 — 2026-05-15

[Release page](https://github.com/vllm-project/vllm/releases/tag/v0.21.0)

- ⚠️ **`transformers` v4 formally deprecated** — migrate to `transformers` v5 (#40389).
- ⚠️ **C++20 build requirement** (breaking build change) for compatibility with PyTorch (#40380).
- KV offloading integrates with the Hybrid Memory Allocator (HMA): scheduler-side sliding window groups, full HMA enablement (#41228, #41445).
- Speculative decoding respects reasoning/thinking budgets for reasoning models (#34668); new TOKENSPEED_MLA attention backend for DeepSeek-R1/Kimi-K25 on Blackwell (#41778).
- New architectures: MiMo-V2.5, Laguna XS.2, Moondream3, Qianfan-OCR, Cohere MoE, Cohere Eagle (#40967, #40136, #40817).
