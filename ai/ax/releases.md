---
upstream: https://github.com/google/ax
last_updated: 2026-09-26
---

# ax — releases

AX is pre-1.0 and the maintainers warn that "major breaking changes [are] likely prior to a stable release." Most releases ship with empty notes, so highlights below are derived from the commit history between tags (linked commit ranges). Release pages: https://github.com/google/ax/releases

## v0.3.1 — 2026-09-25

[Release page](https://github.com/google/ax/releases/tag/v0.3.1) — patch release.

- Removed the `Gateway` concept to avoid bifurcation with Substrate's networking.
- Fixes: task-runner Docker context now includes the runner binaries; `ax get` honors names for plural resources.
- Added an external [roadmap](https://github.com/google/ax/blob/main/docs/roadmap.md) and documented the Agent Substrate prerequisite.

## v0.3.0 — 2026-09-20

[Release page](https://github.com/google/ax/releases/tag/v0.3.0) — **breaking**: major redesign.

- **Breaking**: AX restructured into a general-purpose orchestration layer for agentic tasks — three binaries: `ax-server` (gRPC API), `ax-controller` (horizontally scaled reconcilers consuming Redis Streams), `ax-task-runner` (sandbox entrypoint). Task state moved from Kubernetes CRDs to Redis so etcd is not the bottleneck.
- **Breaking**: legacy Python harness, ATE client, SQL event log, and skill examples removed; `ax.io/v1alpha1` `Task`/`Workspace`/`Model` manifests introduced with new design, concepts, manifests, networking, runner, and sandbox docs plus Kubernetes deploy manifests.
- Added `ax doctor` to diagnose the sidecar, assets, and runtime.

## v0.2.3 — 2026-08-13

[Release page](https://github.com/google/ax/releases/tag/v0.2.3)

- Renamed harness config to `agentConfig` codebase-wide; CLI flags became `--agent-config` / `--agent-config-file`.
- Skills can now load from a local directory alongside the registry; CLI displays tool calls with a loading animation.
- Dependency bumps to fix a broken build.

## v0.2.2 — 2026-07-23

[Release page](https://github.com/google/ax/releases/tag/v0.2.2)

- **Breaking**: CLI simplified — `exec` subcommand removed (flags moved to the root command); `--config` renamed to `--ax-config`.
- Dashboard implementation and web assets removed; protocol `seq` renamed to `step`.
- `ate` package moved out of `internal/k8s` to decouple it from Kubernetes dependencies.

## v0.2.1 — 2026-07-22

[Release page](https://github.com/google/ax/releases/tag/v0.2.1)

- Python sidecar hardening: terminates orphan processes before start and skips startup when the endpoint is already in use.
- Docs cleanup: control-plane terminology corrected, deprecated roadmap items and obsolete harness docs removed.

## v0.2.0 — 2026-07-21

[Release page](https://github.com/google/ax/releases/tag/v0.2.0) — **breaking**: full rewrite of v0.1.0 (424 commits).

- **Breaking**: the project (renamed Agent Executor during this cycle) was rebuilt on top of Agent Substrate actors, replacing the original embedded-harness runtime.
- Antigravity harness sidecar introduced and served over gRPC, with agentic interactions running as Substrate actors and SDK-native resume.
- ADK and A2A agent bridges, skills (including the Gemini Enterprise Skill Registry), and MCP tool support added; `/workspace` became the default working directory on Substrate.

## v0.1.0 — 2026-05-20

[Release page](https://github.com/google/ax/releases/tag/v0.1.0) — initial release.

- A "minimal, robust and opinionated distributed runtime for harnesses and agents easily deployable on Kubernetes," letting users run agentic sessions and extensions on their own data plane.
- Released early "to be able to have some concrete conversations."
