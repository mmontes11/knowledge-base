---
upstream: https://github.com/weave-os/router
last_updated: 2026-09-25
---

# weave-router — releases

Weave Router versions its **software** through git tags and the npm package (`@weave-os/router`, former `@workweave/router` alias) rather than GitHub Releases; the GitHub Releases page is used to publish **model artifacts** for the optional HMM policy sidecar. The Apache-2.0 software release line begins at `router-v0.2.24` (npm `0.2.24`); earlier tags retain the licenses they shipped with. Release pages: https://github.com/weave-os/router/releases.

## hmm-model-v1 — 2026-07-13 (prerelease)

[Release page](https://github.com/weave-os/router/releases/tag/hmm-model-v1) — the frozen HMM policy model artifact.

- Ships the frozen Hidden-Markov-Model routing policy consumed by the optional HMM sidecar (`make up-hmm`).
- Embedding model: Google Gemini Embedding 2, 3072 dimensions. See [HMM sidecar docs](https://github.com/weave-os/router/blob/main/sidecars/hmm/README.md) for artifact verification and embedding compatibility.

No other GitHub Releases are published as of 2026-09-25; check the npm package for the latest software version.
