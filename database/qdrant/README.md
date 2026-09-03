---
upstream: https://github.com/qdrant/qdrant
last_updated: 2026-09-03
---

# qdrant

[Qdrant](https://qdrant.tech/) is an open-source vector similarity search engine and vector database, written in Rust. It stores, indexes, and searches high-dimensional vectors (embeddings) at scale, pairing each vector with structured metadata ("payload") so approximate nearest-neighbor search and exact filtering happen in a single query. It supports dense, sparse, and multimodal (named / multi-vector) collections, hybrid search (dense + sparse + full-text), and a distributed, replicated, and user-sharded deployment model.

- Upstream repository: [qdrant/qdrant](https://github.com/qdrant/qdrant)
- Documentation: [https://qdrant.tech/documentation/](https://qdrant.tech/documentation/)
- Concepts guide: [https://qdrant.tech/documentation/concepts/](https://qdrant.tech/documentation/concepts/)
- gRPC API proto definitions: [lib/api/src/grpc/proto](https://github.com/qdrant/qdrant/tree/master/lib/api/src/grpc/proto)
- Web UI: [qdrant/qdrant-web-ui](https://github.com/qdrant/qdrant-web-ui)
- License: Apache-2.0
- Default ports: REST `6333`, gRPC `6334`, pprof/debug `6335`

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
