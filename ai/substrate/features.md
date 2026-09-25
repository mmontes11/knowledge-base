---
upstream: https://github.com/agent-substrate/substrate
last_updated: 2026-09-25
---

# substrate — features

Agent Substrate is a low-opinion runtime for *running* agents (and other idle-heavy applications) at scale on Kubernetes. It is framework- and agent-harness-agnostic because it manages standard OCI containers at the kernel level. Key capability areas, each linked to the in-repo docs that cover it.

## Actor–worker multiplexing

- **Heavy multiplexing**: maps a large set of actors onto a smaller pool of ready workers, exploiting that agents are idle most of the time; the demo multiplexes ~250 stateful actors across 8 pods (30x+ oversubscription). [Architecture](https://github.com/agent-substrate/substrate/blob/main/docs/architecture.md)
- **Real-time assignment**: assigns actors to workers in real time and routes incoming traffic to them. [API Configuration Guide](https://github.com/agent-substrate/substrate/blob/main/docs/api-guide.md)

## Suspend/resume and state

- **High-performance resume**: sub-500ms resume at 500+ suspend/resume activations per second. [Architecture](https://github.com/agent-substrate/substrate/blob/main/docs/architecture.md)
- **State persistence**: volatile RAM and filesystem state preserved across hibernation cycles via full-state snapshots. [Counter Demo](https://github.com/agent-substrate/substrate/blob/main/demos/counter/README.md)
- **Request parking**: the router holds inbound requests through transient worker-pool saturation instead of returning 503. [Request Parking](https://github.com/agent-substrate/substrate/blob/main/docs/request-parking.md)

## Sandboxing and isolation

- **Multiple sandbox technologies**: microVMs (cloud-hypervisor) and gVisor (runsc), with consistent lifecycle operations across both. [Architecture](https://github.com/agent-substrate/substrate/blob/main/docs/architecture.md)
- **Zero-trust isolation**: native kernel and network isolation; egress policy with protocol allow/block lists and optional MITM interception. [Egress Traffic](https://github.com/agent-substrate/substrate/blob/main/docs/egress-traffic.md) / [MITM interception](https://github.com/agent-substrate/substrate/blob/main/docs/egress-trust-bundle.md)
- **Threat model**: trust boundaries, assumptions, and known risks. [Threat Model](https://github.com/agent-substrate/substrate/blob/main/docs/threat-model.md)

## Kubernetes integration

- **Built on Kubernetes**: Pods and Pod autoscaling for infrastructure provisioning and worker lifecycle, plus agent-specific scheduling and control for lower latency. [Architecture](https://github.com/agent-substrate/substrate/blob/main/docs/architecture.md)
- **Autoscaled WorkerPool**: scales a pool on assigned-worker count with an HPA fed by prometheus-adapter. [Autoscaled WorkerPool demo](https://github.com/agent-substrate/substrate/blob/main/demos/autoscaled-workerpool/README.md)

## Framework compatibility

- Hosts agents built on any stack: **ADK** (session state as actor state), **LangChain**, **Claude Code / CodeX / Antigravity**, and **MCP servers** deployed as sandboxed actors. (README)

## Ecosystem

- **Agent Executor** ([google/ax](https://github.com/google/ax)): a distributed agent runtime built on Substrate.
- **kagent**: a CNCF Kubernetes-native agent framework that uses Substrate for sandboxed, stateful agent workloads.

## Observability

- [Observability Guide](https://github.com/agent-substrate/substrate/blob/main/docs/observability.md): actor logging, metrics, and distributed tracing.
