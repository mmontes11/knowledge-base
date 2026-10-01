---
upstream: https://github.com/comfyanonymous/ComfyUI
last_updated: 2026-10-01
---

# comfyui — Features

Feature areas of ComfyUI as tracked upstream ([README](https://github.com/comfyanonymous/ComfyUI), [docs](https://docs.comfy.org)). The canonical feature list lives in the repo README's "Features" section; this page summarizes the stable, documented areas and links out for details.

## Visual node-graph workflow

The core product: a GPU-powered node editor where each node performs one operation (load model, encode prompt, sample, decode VAE, save image) and connections define data flow. Workflows are plain JSON — save, load, fork, share, or post directly to the API as-is.

- **Subgraphs** (CORE-68): groups of nodes saved as reusable, parameterized subgraphs with their own API surface (see [api-reference.md](api-reference.md)).
- **Generic Loops** (CORE-14, v0.36.0): control-flow loop nodes with loop boundaries declared in the node schema, for iterative processing inside a graph.
- **Workflow library and templates**: built-in workflow-template catalog served at `/api/workflow_templates`, plus a saved-workflow store (`/api/workflows`), so common model recipes (Flux, Wan, LTX, …) are one-click.
- **App mode and node categories**, plus i18n for the frontend.

## Supported models

ComfyUI is model-agnostic by design — one graph runs whatever checkpoint exists in the model folders. Documented upstream support (see the [README model lists](https://github.com/comfyanonymous/ComfyUI#features)):

- **Images**: Stable Diffusion 1/SDXL/SD3, FLUX.1 (fill/kontext/dev), Z-Image, Boogu, krea 2, Hunyuan Image, Lumina 2, NoVA, JoyImageEdit, Qwen-Image 2.1 (v0.37.0), SenseNova U1.5 (v0.35.0), ming-image (v0.38.0).
- **Video**: LTX/LTXV, Wan (+ Wan-Animate2, + Wan 2.1/VACE ID-V2V in v0.38.0), Hunyuan Video, AnimateDiff, Phigent, Sana; LTX 2.5 added in v0.32.0; MiniMax H3 multimodal.
- **Audio** (including MiniMax Music 3 and Yue2 music, v0.36.0) and **3D** (TripoSR, TripoSplat, Pixal3d incl. multiview, TRELLIS2, Sam3d-body, MoGe 3 in v0.37.0).
- **Text/LLM**: Llama-family models via GGUF, Gemma4, Qwen3-VL (as text encoders and for text generation).
- Weight sharing formats: FP16, FP8, NF4, **int8 (convrot)** since v0.27.0, plus **w4a8** (v0.37.0) and **w6a8** (v0.38.0) quantization — a major memory/throughput improvement backed by the `comfy-kitchen` kernel package (NVIDIA + AMD).

## Execution, scheduling, and performance

- **Async queue with partial re-execution**: unchanged nodes are skipped and cached results reused; the queue inspects the graph to find only what changed.
- **Smart offloading and dynamic VRAM**: model weights stream between CPU/GPU (full offload supported for low-VRAM GPUs); pinned-memory MRU weight loading (v0.30.0); WSL dynamic-VRAM fixes.
- **Comfy Compiler** (CORE-389, v0.35.0): compiles model transformer blocks (e.g. Qwen, Flux family) into CUDA graphs for faster sampling.
- **Fast disk**: `--fast-disk` (keep model weights in fast storage) is auto-detected and enabled when the disk is fast (CORE-440, v0.37.0).
- Memory detection respects the container **cgroup memory limit** instead of host RAM (CORE-394, v0.35.0), so Docker/K8s deployments size against the container.
- **Memory management flags** (`--use-sdp-attention`, `--lowvram`, `--highvram`, `--reserve-vram`, …) to tune for the hardware.
- `POST /free` releases model weights on demand; `/system_stats` exposes current GPU/VRAM usage.
- Training: built-in **LoRA/DiT trainer** (CORE-81) now supports video datasets — trained and served from the same instance.

## Customization

- **Custom nodes**: any Python package in `custom_nodes/` that registers node classes extends the UI and the API automatically (`/object_info` reflects new nodes).
- **Model path configuration**: `extra_model_paths.yaml` points multiple ComfyUI installs at shared model trees without copying; `--models-directory` flag (v0.28.0) relocates the top-level model folder.
- **Frontend** is a separate repo — [ComfyUI Frontend](https://github.com/Comfy-Org/ComfyUI_frontend), published to PyPI as `comfyui-frontend-package` and pinned per release; user CSS theming supported.

## API integration and partner nodes

- Full HTTP + WebSocket API (see [api-reference.md](api-reference.md)) with a maintained [OpenAPI 3.0.3 spec](https://github.com/Comfy-Org/ComfyUI/blob/master/openapi.yaml) — the "workflow JSON" format is the client SDK.
- **Partner nodes**: optional first-party nodes for third-party hosted models (OpenAI, Google, ByteDance, xAI, Kling, Ideogram, MiniMax, Bria, Topaz, HeyGen, …) billed through Comfy's backend; disable entirely with `--disable-api-nodes`.
- Multi-user mode with user management, per-user data, and asset tagging; job namespacing with per-job/namespace cancel.

## Media processing toolkit

Built-in post-processing nodes, not just generation: inpainting/outpainting, upscaling, mask and layer compositing (ImageCompositor, bounding-box canvas), image/video blending and frame interpolation, model merging (krea 2 advanced merge), segmentation and depth (SAM, Z-Depth, Marigold v2 in v0.36.0), geometry estimation (MoGe 3, v0.37.0), text overlays, GLSL shaders, video trim/crop/concatenate, video transcode/export with CRF control, and HDR video saving (AV1, mkv, webm) with LogC3/ACEScct color-space conversion (v0.38.0).

## Installation and distribution

- **Comfy Desktop** (Windows/macOS) one-click installer, **Portable Windows** package, and **Comfy Desktop** app for local GPU.
- **Manual install** on Linux/Windows: NVIDIA CUDA (recommended), AMD (ROCm), Intel, Apple Silicon (M/M3/M4), and NPU (MUSA) documented upstream.
- **Comfy Cloud (SaaS)**: hosted inference and CLI at [comfy.org](https://www.comfy.org/) — the same workflow JSON runs cloud or locally.
- Telemetry is off by default and controllable via `--enable-telemetry` (added v0.26.0); core runs fully offline.

## Security posture

Upstream has treated security seriously at scale: four vulnerabilities were fixed together in v0.28.0 ([GHSA-779p-m5rp-r4h4](https://github.com/Comfy-Org/ComfyUI/security/advisories/GHSA-779p-m5rp-r4h4)), stored-XSS mitigations forced safe download previews (v0.30.0), training datasets are sandboxed to a dedicated `dataset` folder (v0.30.0), authorization headers are masked in partner-node logs, and asset hashing is an opt-in capability. For self-hosted deployments, the practical mitigations are single-user mode, no public bind on `--listen`, and keeping `master` current.
