---
upstream: https://github.com/agent-substrate/substrate
last_updated: 2026-10-01
---

# substrate — releases

Agent Substrate is pre-1.0 and ships few, coarse releases. The project states it "will continue to make breaking changes for the immediate future," so treat every release as potentially incompatible with the last. Release pages: https://github.com/agent-substrate/substrate/releases.

## v0.3.0 — 2026-09-30

[Release page](https://github.com/agent-substrate/substrate/releases/tag/v0.3.0) — a fixes-and-polish release that refines egress, identity, and observability ahead of GA.

- **Egress for GA**: egress-policy enhancements and plumbing, e2e tests for egress credential injection, Envoy dynamic-module follow-ups, and `kubectl-ate` gains `delete egress-policy`. [Egress Traffic](https://github.com/agent-substrate/substrate/blob/main/docs/egress-traffic.md)
- **Identity & authorization**: the actor JWT issuer is configurable with a required lifetime aligned to the SPIFFE ID; a runtime authorization check for Atespace CRUD lands (Part 1); the ateom/actor identity split has ateom mint its own actor certificate. [Authentication Guide](https://github.com/agent-substrate/substrate/blob/main/docs/authentication.md)
- **Observability**: the actor usage event and activation epoch are defined, plus SWE-Perf actor telemetry for benchmarking. [Observability Guide](https://github.com/agent-substrate/substrate/blob/main/docs/observability.md)
- **Operations**: CI now creates JUnit artifacts and gates on all tests passing; `ate-setup` adds deploy subcommands for `podcertificate-controller` and `SandboxConfig`; benchmarking logs per-actor checkpoint/restore.

[Full changelog](https://github.com/agent-substrate/substrate/compare/v0.2.0...v0.3.0).

## v0.2.0 — 2026-09-25

[Release page](https://github.com/agent-substrate/substrate/releases/tag/v0.2.0) — adds multi-actor workers, per-sandbox networking, and egress policy enforcement; contains breaking changes to the API and wire protocol, so read before upgrading.

- **Multi-actor workers**: a single worker can now run more than one actor at a time, and each actor sandbox gets its own network namespace. [Architecture](https://github.com/agent-substrate/substrate/blob/main/docs/architecture.md)
- **Lifecycle & state**: a new `RevertActor` lifecycle RPC; `ActorStatus` reports the crash reason and time; ateom can suspend actors itself; actor-template golden snapshots are published as tags. [API Configuration Guide](https://github.com/agent-substrate/substrate/blob/main/docs/api-guide.md)
- **Egress policy enforcement**: the egress gateway now enforces each actor's `EgressPolicy` (an actor with no policy has no egress, and disallowed traffic is denied), plus an egress credential injector with a Kubernetes Secrets credential provider and `kubectl ate` get/create/update egress policies. [Egress Traffic](https://github.com/agent-substrate/substrate/blob/main/docs/egress-traffic.md) / [MITM interception](https://github.com/agent-substrate/substrate/blob/main/docs/egress-trust-bundle.md)
- **Security & identity groundwork**: an OpenFGA model for atespaces (authorization checks not yet enforced); actor JWT and certificate minting move into the API server; running actors receive rotated projected trust bundles without a restart. [Authentication Guide](https://github.com/agent-substrate/substrate/blob/main/docs/authentication.md)
- **Observability**: actor lifecycle events and per-actor usage events are emitted over OTLP. [Observability Guide](https://github.com/agent-substrate/substrate/blob/main/docs/observability.md)
- **Breaking changes**: routing moves from the `Host` header to an explicit `ate-target-actor: <atespace>/<actor>` header; `readyz`→`wakeupProbe`, `IPBlockRule`→`CIDRRule`, `SnapshotsConfig`→`SnapshotConfig`, `LocalSnapshotInfo`→`LocalSnapshot`; every `Delete<Resource>` now takes `DeletePreconditions`; a worker's `sandbox_class` is immutable; actors may no longer send UDP except to DNS.

[Full changelog](https://github.com/agent-substrate/substrate/compare/v0.1.0...v0.2.0).

## v0.1.0 — 2026-09-10

[Release page](https://github.com/agent-substrate/substrate/releases/tag/v0.1.0) — the first "real" release, described as ready for early testing.

- First release cut for early adopters; the maintainers explicitly invite bug reports and feature requests as the system is exercised.
- No API/behavior stability guarantees — breaking changes are expected for the near term.

## v0.0.0 — 2026-05-19

[Release page](https://github.com/agent-substrate/substrate/releases/tag/v0.0.0) — initial commit / placeholder release.
