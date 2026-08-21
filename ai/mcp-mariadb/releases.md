---
upstream: https://github.com/mmontes11/mcp-mariadb
last_updated: 2026-08-21
---

# mcp-mariadb — releases

The upstream repository is not public, so there are no GitHub release notes or release pages. Versions are tracked by the publicly published container image tags on [Docker Hub](https://hub.docker.com/r/mmontes11/mcp-mariadb/tags); bullet facts below additionally draw on the homelab deployment history in [mmontes11/k8s-ai](https://github.com/mmontes11/k8s-ai). There are fewer than 10 releases; all of them are listed, newest first.

## 0.4.0 — 2026-05-03

[Docker Hub tag](https://hub.docker.com/r/mmontes11/mcp-mariadb/tags/item/0.4.0)

- **Latest release**. Multi-arch image (amd64/arm64) on Python 3.11.15; image default transport is `sse` (`CMD ["python", "src/server.py", "--host", "0.0.0.0", "--transport", "sse"]`).
- The homelab cluster updated both `mcp-photoprism-*` instances from `0.3.0` to `0.4.0` on 2026-05-03. The first rollout was reverted and re-applied the same day (k8s-ai commits `594be78` → `a0e0c23` → `84354d8`), i.e. the 0.4.0 image as first pushed had an issue that was resolved by re-publishing. ⚠️ If upgrading from 0.3.0, verify the instance comes back healthy after the image pull (readiness probe on port 9001) before considering the rollout done.

## 0.3.0 — 2026-01-24

[Docker Hub tag](https://hub.docker.com/r/mmontes11/mcp-mariadb/tags/item/0.3.0)

- First image tag deployed to the second homelab instance (`mcp-photoprism-xiaowen`, added 2026-01-25 in k8s-ai).
- Still in service on both homelab instances until the 0.4.0 rollout on 2026-05-03.

## 0.2.0 — 2026-01-24

[Docker Hub tag](https://hub.docker.com/r/mmontes11/mcp-mariadb/tags/item/0.2.0)

- Intermediate tag published and deployed to the first homelab instance (`mcp-photoprism-mmontes`) on the same day (k8s-ai commit `df43ba7`).

## 0.1.0 — 2026-01-24

[Docker Hub tag](https://hub.docker.com/r/mmontes11/mcp-mariadb/tags/item/0.1.0)

- First image tag published to Docker Hub; promoted in the homelab from the initial `0.0.1` deployment the same day (k8s-ai commit `27fa8b7`).

## 0.0.1 — 2026-01-24

Not published to Docker Hub; known only from the initial homelab deployment of `mcp-photoprism-mmontes` (k8s-ai commit `49f6de0`, 2026-01-24). No release notes exist.
