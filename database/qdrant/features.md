---
upstream: https://github.com/qdrant/qdrant
last_updated: 2026-09-03
---

# qdrant — features

Feature areas of Qdrant, each linked to the upstream documentation that covers it. The [concepts guide](https://qdrant.tech/documentation/concepts/), the [search](https://qdrant.tech/documentation/search/search/) section, and the [manage-data](https://qdrant.tech/documentation/manage-data/) section are the authoritative source.

## Vector storage & indexing

- **Dense, sparse, and multi vectors** (`Collection`) — a collection can hold dense vectors, sparse vectors, and multiple named vectors per point, enabling dense+sparse and multimodal (e.g. image + text) workloads. [Vectors](https://qdrant.tech/documentation/concepts/vectors/)
- **Quantization** — scalar, product, and TurboQuant (4-bit / 8x) quantization to cut memory and disk while keeping recall acceptable. [Quantization](https://qdrant.tech/documentation/manage-data/quantization/)
- **Payload indexes** — dedicated indexes (keyword, integer, float, geo, full-text/text, boolean) that speed up filtered search and payload lookups. [Indexing](https://qdrant.tech/documentation/manage-data/indexing/)

## Search

- **ANN & filtered search** — approximate nearest-neighbor search combined with arbitrary metadata filters in a single query. [Search](https://qdrant.tech/documentation/search/search/), [Filtering](https://qdrant.tech/documentation/search/filtering/)
- **Hybrid search** — combine dense, sparse, and full-text scoring, fused with Reciprocal Rank Fusion (including Weighted RRF). [Hybrid queries](https://qdrant.tech/documentation/concepts/hybrid-queries/)
- **Full-text search** — BM25-based text search over text payload fields, with per-tenant IDF statistics. [Full-text search](https://qdrant.tech/documentation/search/text-search/full-text-search/)
- **Recommendation & discover** — recommend points similar to a set of positive/negative examples, and discover points toward "target" examples. [Search relevance](https://qdrant.tech/documentation/concepts/search-relevance/)

## Points & payload

- **Points** — a point pairs a vector (or vectors) with a flexible structured payload; supports upsert, update modes, batch operations, and scroll. [Points](https://qdrant.tech/documentation/concepts/points/)
- **Multitenancy** — per-tenant sharding and payload-based isolation for serving many tenants on one cluster. [Multitenancy](https://qdrant.tech/documentation/manage-data/multitenancy/)

## Deployment & operations

- **Distributed deployment** — scale-out clustering with sharding, replication, and user-defined (shard-key) sharding. [Distributed deployment](https://qdrant.tech/documentation/guides/distributed_deployment/)
- **Consistency & read affinity** — tunable consistency levels and read affinity (routing tokens) for reads. [Consistency guarantees](https://qdrant.tech/documentation/scaling/consistency-guarantees/)
- **Snapshots** — collection and full-cluster snapshots to local storage or S3, with online restore. [Collections](https://qdrant.tech/documentation/manage-data/collections/)
- **Monitoring & telemetry** — Prometheus metrics, a dedicated `/metrics` port, cluster-wide telemetry, and an audit log. [Monitoring](https://qdrant.tech/documentation/guides/monitoring/)
- **Memory tiers & quotas** — per-component memory placement (cold / cached / pinned), a low-memory mode, and a global quota API to bound memory usage. [Memory tiers](https://qdrant.tech/documentation/ops-configuration/memory-tiers/), [Quotas](https://qdrant.tech/documentation/ops-configuration/quotas/)
- **Low-latency search** — deferred updates, an unlimited update queue, and tunable read fan-out to smooth tail latency. [Low-latency search](https://qdrant.tech/documentation/guides/low-latency-search/)

## Client & developer experience

- **Client SDKs** — official Python, Rust, JavaScript/TypeScript, Go, C#, Java, and PHP clients over REST and gRPC. [Documentation](https://qdrant.tech/documentation/)
- **Qdrant Edge** — an in-process library version that shares the server's storage format and point API, for local / on-device use. [Edge](https://qdrant.tech/documentation/edge/)
- **Inference integration** — optional server-side embedding inference via external model providers. [Inference](https://qdrant.tech/documentation/concepts/inference/)
