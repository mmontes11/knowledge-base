---
upstream: https://github.com/comfyanonymous/ComfyUI
last_updated: 2026-10-01
---

# comfyui — Releases

ComfyUI tags versions in `v0.x.y` form on the `master` branch (default branch). Releases are frequent — roughly every 1–3 weeks — combining core sampling/model support, performance work (comfy-kitchen, comfy-aimdo), and third-party "partner" node updates. The ten most recent releases as of 2026-10-01:

## v0.38.0 (2026-09-29)

Release notes: [v0.38.0](https://github.com/comfyanonymous/ComfyUI/releases/tag/v0.38.0)

- ⚠️ **Removes the `torchaudio` dependency** from the core; audio processing no longer requires it.
- Qwen-Image 2.1 support completes (union Fun-ControlNet, tiny VAE, KV-cache and compiled-transformer-block speedups); new ming-image model line; ID-V2V Wan 2.1/VACE identity-preservation video (CORE-426); w6a8 quantization format.
- `TextGenerate` gains a `system_prompt` input and a separate thinking output (CORE-460); MiniMax-H3 Fun-ControlNet-Union 2.0 (CORE-462); partner-node refresh — GPT-6 Sol/Luna, Claude Opus 5.5, Seedream 5.0 Flash + Seedance 2.5 Draft, Recraft V4.1 Flash, Tencent Hunyuan Image 3.5, Quiver Arrow 2 — and removal of the deprecated Sora nodes.

## v0.37.0 (2026-09-21)

Release notes: [v0.37.0](https://github.com/comfyanonymous/ComfyUI/releases/tag/v0.37.0)

- **Fast disk**: `--fast-disk` is now auto-detected and enabled when the disk is fast (CORE-440); comfy-aimdo 0.5.5.
- Qwen-Image 2.1 support (CORE-423); MoGe 3 (CORE-443); CUDA Graphs + w4a8 GEMV for Qwen3/3.5/3.8 (CORE-390).
- Partner nodes: Meshy 7.1, transparent-background output for GPT Image 2; frontend bump to 1.53.6.

## v0.36.0 (2026-09-15)

Release notes: [v0.36.0](https://github.com/comfyanonymous/ComfyUI/releases/tag/v0.36.0)

- **Generic Loops** (CORE-14): loop-control nodes with loop boundaries declared in the node schema (`executionList` marked strictly internal) — control flow inside a graph.
- New nodes: Video Concatenate (CORE-436) and Marigold v2 depth (CORE-431); Yue2 music model support.
- Partner refresh: Tripo migrates to the v3 API with the new Tripo P2 nodes (Smart Segment added, dead widgets retired); GeminiNodeV3 (V2 deprecated); Bria image-edit + Video Eraser; BFL Flux Video Edit; Pruna P-Video-2.

## v0.35.0 (2026-09-09)

Release notes: [v0.35.0](https://github.com/comfyanonymous/ComfyUI/releases/tag/v0.35.0)

- **Comfy Compiler introduced** (CORE-389) — compiles model transformer blocks for faster execution; new Sparse Attention node.
- New models: SenseNova U1.5 (CORE-411), Pixal3D multiview (CORE-421); HDR/high-bit-depth support documented; AVIF output in Save Image Advanced.
- ⚠️ Memory: ComfyUI now respects the container **cgroup memory limit** instead of host RAM (CORE-394) — containerized deployments now size against the container; new VideoTrim/VideoCrop and File3DToMesh nodes; drops the retiring Veo 2/3.0 partner models.

## v0.34.0 (2026-08-26)

Release notes: [v0.34.0](https://github.com/comfyanonymous/ComfyUI/releases/tag/v0.34.0)

- ⚠️ **Python 3.10 reaches end-of-life soon** — upstream now warns at startup to upgrade; plan to move off 3.10.
- MiniMax-H3 grows (new `MiniMaxH3AddGuide` node, per-token video/audio latent noise masks, prompt embeddings, `taeh3` support); 3D generation gains Pixal3d and TRELLIS2 (CORE-278) plus Sam3d-body (CORE-35); HDR video saving with AV1, mkv, and webm and h264 HDR color-space options.
- Partner-node refresh — ByteDance Seedance 2.5 (1080p) / Seedream Fast Mode / Wan 3.0 video, Pixverse V6, Flux Video Upscale, Meshy-7, Gemini 3.7 Flash, FishAudio; removes the Kling v2 image and retired Tripo Refine/v2.0 models; frontend bump to 1.49.6 and a Gemma4 text-generation speedup.

## v0.33.1 (2026-08-13)

Release notes: [v0.33.1](https://github.com/comfyanonymous/ComfyUI/releases/tag/v0.33.1)

- MiniMax H3 music generation support (new model line) with CUDA Graphs support in core.
- Workflow templates updated to v0.11.41; several fixes for nested latents, LTX diffusion decoding, and dynamic-VRAM behavior on WSL.

## v0.32.0 (2026-08-11)

Release notes: [v0.32.0](https://github.com/comfyanonymous/ComfyUI/releases/tag/v0.32.0)

- ⚠️ The **minimum officially supported PyTorch is now 2.7** — earlier 2.x minor versions are no longer guaranteed to work.
- LTX 2.5 model support; MiniMax-H3 VAE and attention via comfy-kitchen; assets list API gains `tags_all`/`tags_any`/`tags_none` filters.
- Frontend bump to 1.48.x and various VRAM/memory fixes (tiled audio decode, nested-tensor latent handling).

## v0.31.0 (2026-08-08)

Release notes: [v0.31.0](https://github.com/comfyanonymous/ComfyUI/releases/tag/v0.31.0)

- Wan-Animate2 video model support (CORE-358); int8_convrot VAE support for MiniMax-H3.
- Speedups for LTX and Wan sampling; ImageCompositor node with layer-state compositing; Linux memory-pin policy fix for systems without swap.

## v0.30.0 (2026-08-03)

Release notes: [v0.30.0](https://github.com/comfyanonymous/ComfyUI/releases/tag/v0.30.0)

- Dedicated `dataset` folder for training data, closing an arbitrary-folder-access path (dataset/segmentation data is now sandboxed to that folder).
- Weight loading into process RAM with an MRU policy using pinned memory; comfy-kitchen AMD support; `/api/jobs` preview-output improvements.

## v0.29.2 (2026-07-31)

Release notes: [v0.29.2](https://github.com/comfyanonymous/ComfyUI/releases/tag/v0.29.2)

- Frontend fixes and new API/partner nodes.
