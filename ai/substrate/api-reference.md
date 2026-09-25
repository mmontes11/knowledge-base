---
upstream: https://github.com/agent-substrate/substrate
last_updated: 2026-09-25
---

# substrate — API reference

Agent Substrate is driven through three surfaces: a **gRPC control-plane API** (`ateapi`) that manages actor and worker lifecycles, the **`kubectl-ate` CLI** for day-2 operations, and a set of **Kubernetes custom resources** reconciled by the `atecontroller`. Inbound data traffic to actors is handled by the `atenet` Envoy routing layer. Substrate is pre-1.0, so none of these APIs carry stability guarantees. The canonical configuration reference is the in-repo [API Configuration Guide](https://github.com/agent-substrate/substrate/blob/main/docs/api-guide.md).

| Surface | Purpose | Upstream docs |
| --- | --- | --- |
| Control plane API (`ateapi`) | gRPC endpoints to create/destroy actors and workers, suspend/resume actors, and assign actors to workers in real time. | [Architecture](https://github.com/agent-substrate/substrate/blob/main/docs/architecture.md) |
| WorkerPool CRD | Declare pools of workers; reconciled by `atecontroller` and autoscaled (HPA) on assigned-worker count. | [API Configuration Guide](https://github.com/agent-substrate/substrate/blob/main/docs/api-guide.md) |
| ActorTemplate CRD | Template defining the actor workload (binary/OCI image, resources) that actors are instantiated from. | [API Configuration Guide](https://github.com/agent-substrate/substrate/blob/main/docs/api-guide.md) |
| Actor | A running instance of a template, with a lifecycle (create/destroy, suspend/resume) and a routing target. | [API Configuration Guide](https://github.com/agent-substrate/substrate/blob/main/docs/api-guide.md) |
| `atenet` (Envoy) | Routes inbound HTTP traffic to actors; supports request parking during worker-pool saturation. | [Request Parking](https://github.com/agent-substrate/substrate/blob/main/docs/request-parking.md) |
| `kubectl-ate` CLI | Manage substrate resources (`create actor`, templates, pools); `go install ./cmd/kubectl-ate`. | [CLI docs](https://github.com/agent-substrate/substrate/blob/main/cmd/kubectl-ate/README.md) |

## Related guides

- [Glossary](https://github.com/agent-substrate/substrate/blob/main/docs/glossary.md) — Actor, Atespace, ActorTemplate, WorkerPool, Worker, `ate-api-server`, `atenet`, `atelet`, `ateom`.
- [Authentication Guide](https://github.com/agent-substrate/substrate/blob/main/docs/authentication.md) — trusted JWT providers and human credentials.
- [Egress Traffic](https://github.com/agent-substrate/substrate/blob/main/docs/egress-traffic.md) — protocols an actor may reach and which are blocked; [MITM interception](https://github.com/agent-substrate/substrate/blob/main/docs/egress-trust-bundle.md) for egress policy.
- [Observability Guide](https://github.com/agent-substrate/substrate/blob/main/docs/observability.md) — actor logging, metrics, and distributed tracing.

## Notes

- Substrate is **pre-1.0**: no backward-compatibility guarantees; APIs and behavior may still change significantly.
- It targets the latest stable Kubernetes release plus the previous minor release.
- Worker capacity is versioned: the dataplane (the `atelet` DaemonSet and worker pods) schedules only on nodes labeled `ate.dev/substrate-version`; nodes added later must be labeled before they host workers.
