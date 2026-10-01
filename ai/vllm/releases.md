---
upstream: https://github.com/vllm-project/vllm
last_updated: 2026-10-01
---

# vllm — releases

Latest 10 official releases, newest first. Check the ⚠️ entries before upgrading. Release cadence is roughly biweekly majors plus occasional patches.

## v0.30.0 — 2026-09-22

[Release page](https://github.com/vllm-project/vllm/releases/tag/v0.30.0)

- **New models**: DeepSeek-V4.1-Flash (full KV in MXFP8 through the FlashMLA V4.1 record on SM100, DeepGEMM Mega-mHC, async Engram prefetch), DeepSeek-V4-Flash-Vision-Exp (ROCm + LoRA), GLM-5.3-Flash (EPLB), K2-Horizon, Cohere Compass, Bailing V3 VL, Nanbeige4.2 via the Transformers backend, and a DeepSeek-V4 CPU backend with AVX512/AMX sparse-MLA kernels (#56214, #56893, #56962, #56512, #54566, #55107, #53906, #55063, #54774, #55921, #56071, #55355).
- **Fast Start**: a persistent per-GPU weight-cache daemon holds post-quantized, TP-sharded weights in GPU memory so restarting engines map them over CUDA IPC with `--load-format ipc_cache` instead of reloading from disk, now incl. FP4 checkpoints and multi-node TP (#54921, #55465, #55468).
- **Watermarking**: Gumbel-max and dual-key Gumbel-max watermarked generation and detection (keyed PRF, per-request opt-out, example detection endpoint); dual-key mode is compatible with speculative decoding (#54053, #56122, #56338).
- **HiSparse**: a host-resident tier for sparse-MLA decode that spills KV pages to pinned host memory under GPU pressure and serves top-k misses from a per-request GPU hot buffer, enabled through `HiSparseConnector` (TP-shared host cache, Prometheus counters) (#53781, #56061, #56629, #57041).
- **Model Runner V2**: dual-batch overlap (eager mode + FULL CUDA graphs), MTP/EAGLE3/DFlash/DSpark speculative decoding under pipeline parallelism, online acceptance estimator for adaptive verification, and GC frozen during graph capture (engine init 28.9s→8.2s on H200) (#50945, #51700, #46994, #50514, #52228, #54646).
- **Large-scale serving & quantization**: PCP+DCP on sparse-MLA models, Elastic EP reusing CUDA graphs across reconfiguration, encoder-cache sharing over NIXL/Mooncake, KVCR secondary-tier adapter; targeted online quantization via `quantization_config.targets` (incl. partially pre-quantized checkpoints), W4A16 DSA with the `nvfp4_fp8_ds_mla` KV cache, NVFP4 W4A16 default over Marlin on SM100/103, AutoRound 2–7-bit on CUDA (#56157, #54985, #47941, #41567, #53624, #51285, #51392, #51724, #53014, #52890).
- ⚠️ **Breaking changes**: scale-out endpoints are opt-in on plain `vllm serve` via `--enable-scale-out` (replaces `VLLM_ENABLE_SCALE_OUT_ENDPOINTS`); GPTQ `g_idx` activation ordering removed; items deprecated in v0.29 removed (incl. `VLLM_PREFIX_CACHE_RETENTION_INTERVAL`, `VLLM_MM_HASHER_ALGORITHM`); `all` Mamba cache mode deprecated; `python -m vllm.entrypoints.grpc_server` deprecated in favor of `vllm serve --grpc`; YaRN no longer re-scales `max_model_len` (#54579, #54809, #55353, #55041, #56746, #56446).
- **API & frontend**: stateless `/v1/responses/render`, `count_reasoning_tokens` usage reporting, site-packages reasoning/tool parser plugins; XGrammar `patternProperties`/`propertyNames`/`unevaluatedProperties`, MCP SDK 2.x schemas, unified Cohere Command parser (#50195, #54982, #45241, #42904, #53870, #56392).

## v0.29.0 — 2026-09-09

[Release page](https://github.com/vllm-project/vllm/releases/tag/v0.29.0)

- ⚠️ **Model Runner V2 is now the default for all models** (#53183), completing the rollout that began with pooling models; **Model Runner V1 is deprecated**, with removal targeted for v0.32 (fallback still used for a few ROCm models and features MRV2 does not yet support: sequence parallelism, dual-batch overlap, elastic EP, custom logits processors, certain speculative decoding methods).
- **New models**: Hy4-preview (Tencent 770B/49B-active MoE, Gated DeepSeek Sparse Attention, native MTP), Qwen3.8-Flash-Next (BF16/FP8/NVFP4 + MTP), GraniteSWA/GraniteMoeSWA, NemotronH_Omni_Reasoning_V3 (+ MTP for Nemotron VL), Kimi K3 NVFP4 checkpoints, FP8 ModernBERT, DeepSeek-backbone embedding models (#54160, #53896, #52706, #52929, #53121, #53132, #53101, #52948).
- **Kimi-K3 / DeepSeek V4 performance**: fused MXFP4 top-k finalization (~5% E2E latency), K3 Mamba metadata prep in one Triton launch (6.6–7.6x kernel speedup), tuned Hopper low-latency GEMM now on SM100 (12.9–25.2% kernel speedup), K3 DCP with DSpark + partial prefix cache hits, DSv4 shared experts fused into MegaMoE, opt-in FlashInfer `moe_ep` expert backend (#53152, #52388, #54088, #53942, #52188, #50493, #53040, #49636).
- **Speculative decoding**: per-request acceptance stats in OpenAI API responses via `--per-request-spec-decode-metrics`, adaptive verification extended to logprobs, SM100 sparse MLA for GLM-5.2 and DSv4-on-SM90, Qwen3-Omni DSpark drafts, PLaMo3 EAGLE-3/DFlash (#48915, #52242, #52783, #52795, #52560, #54239).
- **RL weight sync**: new `sharded_rdt` P2P backend (each worker pulls only its TP/EP slice over NIXL or Ray Direct Transport), rank-local IPC weight updates, sparse checkpoint-coordinate updates through native weight loaders (#43375, #52497, #50723, #53751).
- **New defaults & Mamba prefix caching**: internal prefill checkpoints (9–25% TTFT improvement) with `prefix_cache_retention_interval` now a CLI argument defaulting to 0; FlashInfer all-reduce enabled by default for TP CUDA groups (opt out with `VLLM_ALLREDUCE_USE_FLASHINFER=0`); deterministic `NONE_HASH` prefix cache (no more `PYTHONHASHSEED` pinning); `--max-num-queued-reqs`/`--max-num-queued-tokens` admission control (#52789, #52216, #52998, #51875, #49445).
- ⚠️ **Breaking changes**: ten deprecated model architectures removed (incl. MPT, Arctic, Chameleon, GritLM); FlexOlmo, Olmo3 and Hunyuan V1/VL migrated to the Transformers modeling backend; PyAV video decoder backend removed (use OpenCV or TorchCodec); `python -m vllm.entrypoints.openai.api_server` deprecated in favor of `vllm serve` (#53608, #53615, #54231, #52131).
- **API & frontend**: `/v1/messages/render` and `/cohere/v2/chat/render` render endpoints, video embed inputs, gRPC audio/video inputs + LoRA lifecycle control, pure-Rust `protox` replacing `protoc`; security: `cache_salt` length bounded, oversized media rejected before download, API keys/tokens redacted from logs (#45803, #53219, #54242, #53760, #52840, #52892, #54353, #51896, #52523).

## v0.28.0 — 2026-08-26

[Release page](https://github.com/vllm-project/vllm/releases/tag/v0.28.0)

- **Kimi-K3 performance push** across the stack: Decode Context Parallel (DCP), fused FlashKDA decode/prefill kernels, SiTU activation for MegaMoE, GEMM-RS for sequence parallelism, combined all-gathers with 1.5~3x kernel-level speedup, adaptive speculative token budget (~60% better DSpark TTFT), optional shared-expert sharding (~17 GiB saved per GPU); also runs on ROCm with the V2 model runner (#50484, #50654, #52458, #50510, #52079, #51070, #51725, #50912, #51653).
- **DeepSeek V4**: sparse MLA works end-to-end for plain decode, MTP, and DSpark speculative decoding; AMD Quark NVFP4 support, reasoning-effort prompts, and ROCm enablement on gfx11 and gfx950 (#51538, #47972, #50580, #47017, #52212).
- ⚠️ **Breaking changes**: bitsandbytes support migrated to an out-of-tree plugin; the deprecated `calculate_kv_scales` runtime KV-scale calculation, `override_attention_dtype`, and MoE legacy code removed; KV-offload tiering metrics renamed from `kv_offload_tiering_block_{queries,hits}` to `..._chunk_...` (#43529, #49389, #48684, #51078, #52812).
- ⚠️ **Transformers 5.15.0 upgrade** (breaking environment change); `reasoning_content` output removal is documented as a breaking client change (#51668, #50624).
- **Speculative decoding**: DFlash2 with local convolution and a candidate selector, DSpark confidence-scheduled verification, async scheduling auto-enabled for draft models, adaptive speculative input-token budget (#52816, #47808, #48341, #51725).
- **Model Runner V2 maturation**: E/P/D disaggregation, weight offloading, multi-layer MTP KV cache, encoder CUDA graphs, attention-free models, `thinking_token_budget` support (#38390, #51413, #50062, #49852, #52374, #46727).
- **Tiered KV cache offloading**: disk offloading, out-of-tree secondary-tier managers via `module_path`, tiering metrics, canonical parallelism-agnostic CPU layout; new defaults — `max_num_batched_tokens` raised 8192→16384, prefix caching on by default for Mamba models, Blackwell CUDA graph capture default raised to 1024 (#49644, #51007, #48798, #48414, #51726, #50991, #49390).
- New models: Muse Glimmer, Ling 3.0 Flash (BF16 + MTP + parser, FP8 variant, hybrid MXFP4 routed experts), Dots3 NOTE native multimodal, Interns2mobius (#51655, #51045, #51265, #52114, #51255, #51149).
- **Rust frontend & gRPC**: standalone renderer, multimodal image inference over gRPC, explicit data-parallel rank routing, RL lifecycle control; protobuf schemas published to Buf (#50289, #50368, #51178, #51316, #51276).

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
