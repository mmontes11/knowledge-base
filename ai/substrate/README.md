---
upstream: https://github.com/agent-substrate/substrate
last_updated: 2026-09-25
---

# substrate

Agent Substrate is a secure-by-default agent execution runtime engineered to run millions of sandboxes at 10x the density of standard container runtimes. It multiplexes a large set of idle-most-of-the-time "actors" (agents and other applications) onto a small pool of ready "workers", delivering sub-500ms resume at 500+ suspend/resume activations per second with native zero-trust kernel and network isolation. It supports both microVM and gVisor sandboxes, manages an actor's lifecycle (create/destroy, suspend/resume), assigns actors to workers in real time, and routes inbound traffic to them. Substrate is a low-opinion system for *running* agents at scale (not an SDK for building them), and it leverages Kubernetes — Pods and Pod autoscaling — for infrastructure provisioning and worker lifecycle management.

- Upstream repository: https://github.com/agent-substrate/substrate
- Documentation: in-repo `docs/` — [Architecture](https://github.com/agent-substrate/substrate/blob/main/docs/architecture.md), [API Configuration Guide](https://github.com/agent-substrate/substrate/blob/main/docs/api-guide.md), [Glossary](https://github.com/agent-substrate/substrate/blob/main/docs/glossary.md)
- YouTube channel: https://www.youtube.com/channel/UCN9PPqlTtVxlcpbQ-NWpfZQ
- Community: [ate-dev Google Group](https://groups.google.com/g/ate-dev); CNCF Slack `#substrate-users` / `#substrate-dev`
- License: Apache-2.0
- Stack: Go; gRPC control plane (`ateapi`), node-level `atelet` DaemonSet, `atenet` Envoy routing, `kubectl-ate` CLI; microVM (cloud-hypervisor) + gVisor (runsc) sandboxes on Kubernetes

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
