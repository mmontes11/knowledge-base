---
upstream: https://github.com/google/ax
last_updated: 2026-09-26
---

# ax

AX is a high-throughput, declarative orchestrator for running billions of autonomous agent workloads in a Kubernetes cluster. Everything is expressed as `ax.io/v1alpha1` manifests (`Task`, `Workspace`, `Model`) and applied with a `kubectl`-shaped CLI; each task is scheduled as a sandboxed actor on [Agent Substrate](https://github.com/agent-substrate/substrate), while task state lives in Redis (hashes + event streams) so etcd is not strained by millions of short-lived tasks. AX is pre-stable: the maintainers warn that major breaking changes are expected prior to a stable release.

- Upstream repository: https://github.com/google/ax
- Documentation: in-repo `docs/` — [Concepts](https://github.com/google/ax/blob/main/docs/concepts.md), [Manifests](https://github.com/google/ax/blob/main/docs/manifests.md), [Sandbox](https://github.com/google/ax/blob/main/docs/sandbox.md), [Runners](https://github.com/google/ax/blob/main/docs/runner.md), [Networking](https://github.com/google/ax/blob/main/docs/networking.md), [Roadmap](https://github.com/google/ax/blob/main/docs/roadmap.md)
- Architecture: [DESIGN.md](https://github.com/google/ax/blob/main/DESIGN.md)
- License: Apache-2.0
- Stack: Go; `ax` CLI, stateless `ax-server` (gRPC), horizontally scaled `ax-controller` reconcilers (Redis Streams), `ax-task-runner` (PID 1 inside every task sandbox); built on Agent Substrate

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
