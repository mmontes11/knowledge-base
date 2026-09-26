---
upstream: https://github.com/google/ax
last_updated: 2026-09-26
---

# ax — features

AX is a declarative orchestrator for agent workloads at cluster scale, built on Agent Substrate for sandboxed execution. Key capability areas, each linked to the upstream docs that cover it.

## Declarative task management

- **Task primitive**: one cheap, isolated, suspendable unit of execution — an agent composes as many tasks as its work demands (a task may be the whole job or the root of a task tree). [Concepts](https://github.com/google/ax/blob/main/docs/concepts.md#task)
- **Phases and conditions**: `status.phase` (`Running`, `Suspended`, `Failed`, `Terminating`, ...) plus `WorkspaceReady`/`Ready` conditions, streamed live with `ax watch`. [Concepts](https://github.com/google/ax/blob/main/docs/concepts.md#lifecycle)
- **Suspend/resume**: `ax suspend` checkpoints actor state and pauses; `ax resume` picks up exactly where the task left off. [README](https://github.com/google/ax)
- **Atespace scoping**: every resource lives in an atespace (default `default`), selected with the CLI `-a` flag. [Concepts](https://github.com/google/ax/blob/main/docs/concepts.md)

## Workspace preparation

- **Git repos, MCP servers, skills**: a `Workspace` declares Git repos (repo + branch), MCP servers and registries, and skill registries with a materialization path; bind it to any number of tasks and the runner materializes it in each sandbox before the command starts. [Manifests](https://github.com/google/ax/blob/main/docs/manifests.md#workspace)
- **Goal-driven bootstrap**: a binding `goal` is handed to an agent that finishes environment setup (toolchains, dependencies) on first boot, so the task's command starts in a ready environment. [Sandbox](https://github.com/google/ax/blob/main/docs/sandbox.md)

## Sandboxing and isolation

- **Runs on Agent Substrate**: every task is a sandboxed actor with CPU/memory limits and Substrate's zero-trust kernel and network isolation (microVM or gVisor). [README](https://github.com/google/ax#prerequisites) / [Substrate](https://github.com/agent-substrate/substrate)
- **Metadata server**: in-sandbox HTTP daemon (port 80) serving `/healthz`, `/readyz`, and the task/workspace launch configuration as YAML — no SDK needed to introspect your own environment. [Sandbox](https://github.com/google/ax/blob/main/docs/sandbox.md#metadata-server)
- **Debug guest services**: with `spec.debug: true` the sandbox serves Agent Substrate guest services (arbitrary process execution and file access); off by default, and `ax ssh` refuses tasks that have not enabled them. [Sandbox](https://github.com/google/ax/blob/main/docs/sandbox.md#guest-services)
- **Custom runners**: extend the default `ax-task-runner` image, embed the `runner` package in your own binary, or write a runner from scratch against a documented contract. [Runners](https://github.com/google/ax/blob/main/docs/runner.md)

## Control plane design

- **Redis-backed state**: task hashes, event streams, and pub/sub in Redis; `ax-server` publishes, a horizontally scaled `ax-controller` pool consumes via Redis Streams (XREADGROUP) — designed for millions of short-lived tasks without straining etcd. [DESIGN.md](https://github.com/google/ax/blob/main/DESIGN.md#architecture)
- **kubectx-aware CLI**: `ax` follows the active Kubernetes context and tunnels to that cluster's control plane in the background; `--context` targets a context without switching. [README — CLI usage](https://github.com/google/ax#works-with-kubectx)
- **Networking through atenet**: tasks get no Kubernetes Service or Ingress; requests go through Substrate's `atenet-router` with an `ate-target-actor: <atespace>/<task>` header, from in-cluster, a port-forward, or gRPC metadata. [Networking](https://github.com/google/ax/blob/main/docs/networking.md)

## Model configuration

- **Model resource**: provider, model identifier, generation parameters, and a Kubernetes secret reference for credentials (`google` and `anthropic` providers documented); rotating a key or pinning a model version is a single `ax apply`. [Manifests](https://github.com/google/ax/blob/main/docs/manifests.md#model)

## Pre-stable status

- Core concepts, protocols, and specs are still being refined; major breaking changes are expected before a stable release. [README](https://github.com/google/ax)
- [Roadmap](https://github.com/google/ax/blob/main/docs/roadmap.md): stabilize core specs (including planned `Sandbox`/`SandboxConfig` kinds and token/timeout budgets), actor architecture (dedicated workspace-setup actor, idle detection with automatic suspension, stateful task branching), dynamic agentic environment curation, SPIFFE task identity, and OpenTelemetry metrics/traces/trajectory collection.
