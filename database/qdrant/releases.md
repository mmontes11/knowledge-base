---
upstream: https://github.com/qdrant/qdrant
last_updated: 2026-09-03
---

# qdrant — releases

Latest 10 official releases of the `qdrant/qdrant` project, newest first. Scan the ⚠️ entries before upgrading — Qdrant changes its storage internals and gRPC wire format across minor versions, so upgrade one minor at a time rather than skipping ahead.

## v1.19.0 — 2026-08-05
[Release page](https://github.com/qdrant/qdrant/releases/tag/v1.19.0)
- **TurboQuant 4-bit** — a new primary-vector datatype that stores only 4-bit quantized vectors to spare disk ([docs](https://qdrant.tech/documentation/manage-data/vectors/)).
- **Unified memory tiers** — per-component `"memory": "cold" / "cached" / "pinned"` for fine-grained memory control ([docs](https://qdrant.tech/documentation/ops-configuration/memory-tiers/)).
- **Prefix match** (`"match": {"prefix": "..."}`), **per-query IDF** for sparse full-text search, and a **slice filtering** condition for sliced scroll / deterministic sampling.
- **Global quota API** and a **routing token** for deterministic read affinity ([docs](https://qdrant.tech/documentation/scaling/consistency-guarantees/)).
- ⚠️ **Deprecations**: deprecated search endpoints removed from OpenAPI (kept but deprecated in gRPC); `on_disk_payload` now optional; strict-mode `max_resident_memory_percent` deprecated in favor of the global quota API.

## v1.18.3 — 2026-07-17
[Release page](https://github.com/qdrant/qdrant/releases/tag/v1.18.3)
- **Fix**: query errors when using shard keys while resharding.

## v1.18.2 — 2026-06-04
[Release page](https://github.com/qdrant/qdrant/releases/tag/v1.18.2)
- **Fixes**: optimizer infinite loop on multi-vectors with `prevent_unoptimized`; non-idempotent abort transfer during resharding; WAL lock error on Android platforms.
- **Security**: REST auth whitelist bypass on crafted paths (route now resolved before authorizing); out-of-bounds heap read from a malicious snapshot.

## v1.18.1 — 2026-05-22
[Release page](https://github.com/qdrant/qdrant/releases/tag/v1.18.1)
- **Fixes**: indexed integer range filter with float values; `{match: {except: []}}` returning zero results; resharding cleanup data race with the update queue.
- **Security**: authorize the request before accepting a snapshot file upload.

## v1.18.0 — 2026-05-11
[Release page](https://github.com/qdrant/qdrant/releases/tag/v1.18.0)
- **Features**: TurboQuant quantization variant (8x compression without recall tax), API to create/delete named vectors, deep memory reporting, low-memory mode, and a strict mode (`max_resident_memory_percent`).
- ⚠️ **RocksDB support fully removed** in favor of Gridstore — a direct upgrade from v1.15.x is not supported; upgrade one minor at a time.
- **Security**: API key/JWT auth enforced on internal gRPC endpoints; option to disable snapshot restore from URL.

## v1.17.1 — 2026-03-27
[Release page](https://github.com/qdrant/qdrant/releases/tag/v1.17.1)
- **Improvements**: deferred point updates with `prevent_unoptimized`, non-blocking Gridstore flushes, and request tracing ID in the audit log.
- **Fixes**: tiered multi-tenancy shard-key creation, WAL replay panics, and a security patch restricting snapshot recovery to the snapshot directory.

## v1.17.0 — 2026-02-20
[Release page](https://github.com/qdrant/qdrant/releases/tag/v1.17.0)
- **Features**: Relevance Feedback, detailed optimization-progress API, cluster-wide telemetry, unlimited update queue, audit access logging, Weighted RRF, `update_mode` for upserts, secondary API-key rotation, and a dedicated `/metrics` HTTP port.
- ⚠️ **gRPC wire format**: vector-field response format changed — upgrade client libraries. Upcoming: deprecated search methods removed in v1.18.x, RocksDB removed in v1.17.x.

## v1.16.3 — 2025-12-19
[Release page](https://github.com/qdrant/qdrant/releases/tag/v1.16.3)
- **Fixes**: WAL delta transfer corrupting a replica after an aborted full transfer; flush losing changes on transient disk IO errors; Gridstore flush data races; RocksDB support retained until 1.18.0 in dev builds.

## v1.16.2 — 2025-12-04
[Release page](https://github.com/qdrant/qdrant/releases/tag/v1.16.2)
- **Fixes**: a critical WAL bug that could break consensus or corrupt data on restart; consensus crash applying a snapshot with a non-replicated collection; payload-index and Gridstore flush-after-removal data corruption.

## v1.16.1 — 2025-11-25
[Release page](https://github.com/qdrant/qdrant/releases/tag/v1.16.1)
- **Improvements**: batch queries up to 3x faster on full scans; automatic RocksDB→Gridstore storage migration on startup; user-configurable inference timeout.
- **Fixes**: startup panic on old user-sharded clusters, Raft crash loop, and a WAL corruption edge case.
