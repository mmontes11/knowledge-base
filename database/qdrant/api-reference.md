---
upstream: https://github.com/qdrant/qdrant
last_updated: 2026-09-03
---

# qdrant — API reference

Qdrant is a non-Kubernetes project: its API surface is the pair of HTTP endpoints the server exposes (REST and gRPC) plus the official client SDKs that wrap them. REST is JSON over HTTP (port `6333`); gRPC (port `6334`) is the same operations as a higher-throughput binary protocol, with its types defined in the [proto files under `lib/api/src/grpc/proto`](https://github.com/qdrant/qdrant/tree/master/lib/api/src/grpc/proto). This table links, it does not copy.

| Surface | Purpose | Upstream docs |
| ------- | ------- | ------------- |
| REST API (JSON, port `6333`) | The primary API: collections, points, search/query, filters, snapshots, and cluster operations, all as JSON over HTTP. | [Documentation](https://qdrant.tech/documentation/) |
| gRPC API (port `6334`) | The same operations as a higher-throughput binary protocol; the proto definitions are the source of truth for request/response types. | [proto: lib/api/src/grpc/proto](https://github.com/qdrant/qdrant/tree/master/lib/api/src/grpc/proto) |
| Points & vectors | Create/read/update/delete points, set named / dense / sparse / multi vectors, and scroll. | [Points](https://qdrant.tech/documentation/concepts/points/), [Vectors](https://qdrant.tech/documentation/concepts/vectors/) |
| Search & query | ANN search, hybrid (dense + sparse) queries, recommendation/discover, and full-text search. | [Search](https://qdrant.tech/documentation/search/search/), [Hybrid queries](https://qdrant.tech/documentation/concepts/hybrid-queries/), [Full-text search](https://qdrant.tech/documentation/search/text-search/full-text-search/) |
| Filtering | Combine search with match, range, geo, full-text, nested, prefix, `min_should`, and `slice` conditions. | [Filtering](https://qdrant.tech/documentation/search/filtering/) |
| Collections | Create/update/delete collections and aliases; configure sharding, replication, and on-disk layout. | [Collections](https://qdrant.tech/documentation/manage-data/collections/) |
| Payload & indexing | Structured metadata and payload indexes (keyword, integer, float, geo, full-text/text, boolean). | [Payload](https://qdrant.tech/documentation/manage-data/payload/), [Indexing](https://qdrant.tech/documentation/manage-data/indexing/) |
| Quantization | Scalar, product, and TurboQuant quantization to shrink memory and disk footprint. | [Quantization](https://qdrant.tech/documentation/manage-data/quantization/) |
| Snapshots | Create, list, and restore collection and full-cluster snapshots (local or S3). | [Collections](https://qdrant.tech/documentation/manage-data/collections/) |
| Client SDKs | Official clients for Python, Rust, JavaScript/TypeScript, Go, C#, Java, and PHP wrapping REST and gRPC. | [Documentation](https://qdrant.tech/documentation/) |

Notes:

- The REST and gRPC APIs are functionally equivalent; gRPC is preferred for high-throughput ingest and search. Treat the [proto files](https://github.com/qdrant/qdrant/tree/master/lib/api/src/grpc/proto) as canonical when the two diverge on a field.
- The Web UI is a separate, browser-based tool for inspecting collections, points, and search results; see [qdrant-web-ui](https://github.com/qdrant/qdrant-web-ui).
