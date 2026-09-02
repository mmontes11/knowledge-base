---
upstream: https://github.com/comfyanonymous/ComfyUI
last_updated: 2026-08-18
---

# comfyui — Releases

ComfyUI tags versions in `v0.x.y` form on the `master` branch (default branch). Releases are frequent — roughly every 1–3 weeks — combining core sampling/model support, performance work (comfy-kitchen, comfy-aimdo), and third-party "partner" node updates. The ten most recent releases as of 2026-08-18:

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

## v0.29.0 (2026-07-29)

Release notes: [v0.29.0](https://github.com/comfyanonymous/ComfyUI/releases/tag/v0.29.0)

- **Trainer video support (CORE-81)**: video processing nodes, video support in image-processing nodes, and LoRA/DiT datasets built from video.
- Streamed video transcode (no full-frame RAM buffering); Gemma4 12B and JoyImageEdit native model support; native Uni3C ControlNet for Wan models; numerous partner-node additions (GPT-5.6 class LLMs, Gemini 3.5 Flash, HeyGen).

## v0.28.0 (2026-07-15)

Release notes: [v0.28.0](https://github.com/comfyanonymous/ComfyUI/releases/tag/v0.28.0)

- ⚠️ **Falls out of Python/PyTorch guarantee**: drops PyTorch 2.4 support (GQA works on all attention backends); ⚠️ **removes** the Ideogram and StabilityAI partner-node families.
- Security release: fixes four vulnerabilities in one advisory ([GHSA-779p-m5rp-r4h4](https://github.com/Comfy-Org/ComfyUI/security/advisories/GHSA-779p-m5rp-r4h4)).

## v0.27.0 (2026-06-30)

Release notes: [v0.27.0](https://github.com/comfyanonymous/ComfyUI/releases/tag/v0.27.0)

- Big performance release: **int8 (convrot) model support** — int8 checkpoints run faster and use less memory, including int8 LoRA application and Turing-generation GPUs.
- Model weights can now be loaded from a `models/` directory on non-default model paths; bounding-box canvas and asset hashing introduced.

## v0.26.0 (2026-06-23)

Release notes: [v0.26.0](https://github.com/comfyanonymous/ComfyUI/releases/tag/v0.26.0)

- New job-management surface: `POST /api/jobs/{job_id}/cancel` and `POST /api/jobs/cancel` (cancel one job or all non-running jobs in a namespace).
- New model support (SCAIL-2 multireference, Qwen3-VL as text encoders, Boogu-Image); node renames and model blueprint updates.

## v0.25.1 (2026-06-18)

Release notes: [v0.25.1](https://github.com/comfyanonymous/ComfyUI/releases/tag/v0.25.1)

- Point release: adds support for the Kling V3-Turbo model in the partner-node set.
