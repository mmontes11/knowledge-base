# knowledge-base

A curated knowledge base of open-source projects available on GitHub. Each tracked project is documented as a small, fixed set of Markdown files, and an AI agent runs periodically to keep the folder structure and the content up to date.

This README is the **contract of the knowledge base**: it defines the layout, the naming conventions, the standard documents, and the maintenance rules that both humans and the maintenance agent must follow. Anything in this repository that contradicts this document is the thing that is wrong.

## Goals

- Answer, per project, four questions from one stable place: *what is it*, *what is its API surface*, *what is the latest release*, and *what features does it have*.
- Keep the structure **uniform across all projects** so it can be read and queried mechanically.
- Keep the content **verifiable**: every factual statement carries a link to the upstream source.
- Keep updates **idempotent and reviewable**: one agent run produces a small, explainable diff per project.

## Repository layout

```
.
├── README.md                  # This file: the rules of the knowledge base
├── LICENSE
└── <category>/                # root category folder (lowercase, kebab-case)
    ├── README.md              # short description of the category
    └── <project>/             # one folder per tracked upstream repository
        ├── README.md          # project overview
        ├── api-reference.md   # API reference
        ├── releases.md        # latest releases
        └── features.md        # feature documentation
```

### Categories

- A **category** is a root-level folder that groups projects by domain, e.g. `kubernetes/` or (planned) `database/`.
- Category names: lowercase, kebab-case.
- Every category has a `README.md` with a short description of the domain. The list of tracked projects lives only in [Tracked projects](#tracked-projects) in this root `README.md`.
- Adding a category = create the root folder + its `README.md`, then add the row to [Tracked projects](#tracked-projects) below.

### Project folders

- A **project** is a single upstream repository, documented in one dedicated folder under its category.
- The folder name is the upstream **repository name** (lowercase, kebab-case). Example: `kubernetes/mariadb-operator` documents the `mariadb-operator/mariadb-operator` repository.
- Every project folder contains exactly the four **standard documents** below. Additional material (extended notes, exports, digressions) goes in subfolders that carry their own `README.md`, so the top level stays stable and machine-readable.

## Standard documents

All four files start with the [frontmatter](#metadata) block.

### 1. `README.md` — project overview

- One short paragraph: what the project is and what problem it solves. No marketing.
- Facts: upstream repository URL, documentation site, license.
- Links to the other three standard documents of the folder.

### 2. `api-reference.md` — API surface

- For Kubernetes projects: a table of the resource/CRD catalog. One row per Kind: API group/version, one-line purpose, and a link to the upstream API docs for that Kind.
- For non-Kubernetes projects: the same table shape for the main surfaces (CLI commands, HTTP endpoints, client SDKs).
- **Link, don't copy**: never inline full schemas, OpenAPI specs, or generated reference dumps — link to the canonical upstream documentation.

### 3. `releases.md` — latest releases

- The **latest 10 releases**, most recent first.
- Per entry: tag, release date, link to the release page on GitHub, and 1–3 bullets with the highlights (breaking changes, major additions, removals).
- Breaking changes are called out explicitly; this file is the first thing an operator reads before an upgrade.

### 4. `features.md` — feature documentation

- The significant feature areas of the project (e.g. replication, backup, failover), each with a short paragraph: what it does and a link to the upstream documentation or the release that introduced it.
- When a feature is removed in a newer major version, mark it as *removed in vX.Y* instead of silently deleting it.

## Metadata

Every standard document begins with YAML frontmatter:

```yaml
---
upstream: https://github.com/mariadb-operator/mariadb-operator
last_updated: 2026-08-15
---
```

- `upstream`: canonical repository URL. Required.
- `last_updated`: UTC date the content was last verified or changed. It is the only staleness signal the agent relies on; files older than the re-check threshold (default: **90 days**) are re-verified on the next run even if no new release appeared.

## Maintenance rules (AI agent)

The agent runs on a periodic schedule (operator-configured, e.g. weekly). Every run must be **idempotent**: unchanged upstream state produces an empty diff.

For each tracked project, in order:

1. Read the current folder and frontmatter.
2. Fetch upstream truth: latest release tag, releases newer than the newest entry in `releases.md`, and changes to the API/feature documentation pages.
3. Update `releases.md` (prepend new entries, keep the 10-entry cap).
4. Update `api-reference.md` only if the API surface changed (new/removed/renamed Kinds or surfaces).
5. Update `features.md` only if feature documentation or release notes changed.
6. Update the project `README.md` only if description, license, or upstream URLs changed.
7. Bump `last_updated` in the frontmatter of every file that was modified.
8. Commit once per project with a conventional message: `docs(<category>/<project>): <summary>` (e.g. `docs(kubernetes/mariadb-operator): add release v1.5.0`).
9. Open a pull request against `main`; a human reviews and merges.

**Adding a project**

1. Create `<category>/<project>/` with the four standard documents, populated from upstream.
2. Add the row to [Tracked projects](#tracked-projects).

**Adding a category**

1. Create the root folder and its `README.md`.
2. Add the row to [Tracked projects](#tracked-projects).

**Hygiene rules**

- No credentials, tokens, or cluster-specific data — ever.
- Summarize and link; do not copy upstream text wholesale (respect upstream licenses and attribution).
- Every factual statement links to the upstream source it came from.

## Tracked projects

| Category     | Project                                                  | Upstream                                                                                      |
| ------------- | --------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| `kubernetes` | [cert-manager](kubernetes/cert-manager/) | [jetstack/cert-manager](https://github.com/jetstack/cert-manager) |
| `kubernetes` | [charts](kubernetes/charts/)             | [mmontes11/charts](https://github.com/mmontes11/charts)           |
| `kubernetes` | [cloudnative-pg](kubernetes/cloudnative-pg/) | [cloudnative-pg/cloudnative-pg](https://github.com/cloudnative-pg/cloudnative-pg) |
| `ai`         | [comfyui](ai/comfyui/)                       | [comfyanonymous/ComfyUI](https://github.com/comfyanonymous/ComfyUI)               |
| `kubernetes` | [csi-driver-nfs](kubernetes/csi-driver-nfs/) | [kubernetes-csi/csi-driver-nfs](https://github.com/kubernetes-csi/csi-driver-nfs) |
| `kubernetes` | [external-snapshotter](kubernetes/external-snapshotter/) | [kubernetes-csi/external-snapshotter](https://github.com/kubernetes-csi/external-snapshotter) |
| `kubernetes` | [gateway-api](kubernetes/gateway-api/)                   | [kubernetes-sigs/gateway-api](https://github.com/kubernetes-sigs/gateway-api)                 |
| `ai`         | [github-mcp-server](ai/github-mcp-server/)               | [github/github-mcp-server](https://github.com/github/github-mcp-server)                       |
| `ai`         | [kserve](ai/kserve/)                                     | [kserve/kserve](https://github.com/kserve/kserve)                                             |
| `ai`         | [kubernetes-mcp-server](ai/kubernetes-mcp-server/)       | [containers/kubernetes-mcp-server](https://github.com/containers/kubernetes-mcp-server)       |
| `ai`         | [llama-cpp](ai/llama-cpp/)                               | [ggml-org/llama.cpp](https://github.com/ggml-org/llama.cpp)                                   |
| `ai`         | [lws](ai/lws/)                                           | [kubernetes-sigs/lws](https://github.com/kubernetes-sigs/lws)                                 |
| `kubernetes` | [mariadb-operator](kubernetes/mariadb-operator/)         | [mariadb-operator/mariadb-operator](https://github.com/mariadb-operator/mariadb-operator)     |
| `ai`         | [mcp-grafana](ai/mcp-grafana/)                           | [grafana/mcp-grafana](https://github.com/grafana/mcp-grafana)                                 |
| `ai`         | [mcp-mariadb](ai/mcp-mariadb/)                           | [mmontes11/mcp-mariadb](https://github.com/mmontes11/mcp-mariadb)                             |
| `kubernetes` | [metallb](kubernetes/metallb/)                           | [metallb/metallb](https://github.com/metallb/metallb)                                         |
| `kubernetes` | [metrics-server](kubernetes/metrics-server/)             | [kubernetes-sigs/metrics-server](https://github.com/kubernetes-sigs/metrics-server)           |
| `ai`         | [multica](ai/multica/)                                   | [multica-ai/multica](https://github.com/multica-ai/multica)                                   |
| `ai`         | [n8n](ai/n8n/)                                           | [n8n-io/n8n](https://github.com/n8n-io/n8n)                                                   |
| `kubernetes` | [nvidia-dcgm-exporter](kubernetes/nvidia-dcgm-exporter/) | [NVIDIA/dcgm-exporter](https://github.com/NVIDIA/dcgm-exporter)                               |
| `kubernetes` | [nvidia-device-plugin](kubernetes/nvidia-device-plugin/) | [NVIDIA/k8s-device-plugin](https://github.com/NVIDIA/k8s-device-plugin)                       |
| `ai`         | [open-webui](ai/open-webui/)                             | [open-webui/open-webui](https://github.com/open-webui/open-webui)                             |
| `ai`         | [opencode](ai/opencode/)                                 | [anomalyco/opencode](https://github.com/anomalyco/opencode)                                   |
| `kubernetes` | [redis-operator](kubernetes/redis-operator/)             | [OT-CONTAINER-KIT/redis-operator](https://github.com/OT-CONTAINER-KIT/redis-operator)         |
| `kubernetes` | [rook](kubernetes/rook/)                                 | [rook/rook](https://github.com/rook/rook)                                                     |
| `kubernetes` | [sealed-secrets](kubernetes/sealed-secrets/)             | [bitnami-labs/sealed-secrets](https://github.com/bitnami-labs/sealed-secrets)                 |
| `kubernetes` | [seaweedfs-operator](kubernetes/seaweedfs-operator/)     | [seaweedfs/seaweedfs-operator](https://github.com/seaweedfs/seaweedfs-operator)               |
| `kubernetes` | [snapscheduler](kubernetes/snapscheduler/)               | [backube/snapscheduler](https://github.com/backube/snapscheduler)                             |
| `kubernetes` | [synology-csi](kubernetes/synology-csi/)                 | [SynologyOpenSource/synology-csi](https://github.com/SynologyOpenSource/synology-csi)         |
| `kubernetes` | [tailscale](kubernetes/tailscale/)                       | [tailscale/tailscale](https://github.com/tailscale/tailscale)                                 |
| `kubernetes` | [topolvm](kubernetes/topolvm/)                           | [topolvm/topolvm](https://github.com/topolvm/topolvm)                                         |
| `kubernetes` | [traefik](kubernetes/traefik/)                           | [traefik/traefik](https://github.com/traefik/traefik)                                         |
| `kubernetes` | [trust-manager](kubernetes/trust-manager/)               | [cert-manager/trust-manager](https://github.com/cert-manager/trust-manager)                   |
| `ai`         | [vllm](ai/vllm/)                                         | [vllm-project/vllm](https://github.com/vllm-project/vllm)                                     |
| `kubernetes` | [volsync](kubernetes/volsync/)                           | [backube/volsync](https://github.com/backube/volsync)                                         |

> The `kubernetes/` tree is bootstrapped by the first maintenance run: the folder, its `README.md`, and the four standard documents for `mariadb-operator` are created and populated from upstream.
