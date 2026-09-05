---
upstream: https://github.com/vitessio/vitess
last_updated: 2026-09-05
---

# vitess

[Vitess](https://vitess.io/) is a scalable MySQL database cluster and proxy system, written in Go. It lets you scale a single logical MySQL database across many physical shards (keyspaces and shards) while remaining transparent to application clients: applications connect to Vitess as if it were one MySQL server, and Vitess handles sharding, query routing, and replication. Vitess is designed for cloud-native, horizontally scalable deployments and is the engine behind PlanetScale.

- Upstream repository: [vitessio/vitess](https://github.com/vitessio/vitess)
- Documentation: [https://vitess.io/docs/](https://vitess.io/docs/)
- v24.0 docs: [https://vitess.io/docs/24.0/](https://vitess.io/docs/24.0/)
- Concepts guide: [https://vitess.io/docs/24.0/concepts/](https://vitess.io/docs/24.0/concepts/)
- Get started with the operator: [https://vitess.io/docs/24.0/get-started/operator/](https://vitess.io/docs/24.0/get-started/operator/)
- License: Apache-2.0
- Written in: Go

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)

## Vitess-specific maintenance rules (AI agent)

The generic rules in the root [README](../../README.md#maintenance-rules-ai-agent) apply unchanged (idempotent runs, update each standard document only when its upstream truth changed, `last_updated` bumps, one conventional commit per project, direct push to `main`, hygiene). The rules below are Vitess-specific deltas; where they differ, they override the generic rules for this folder.

### Release trains, not one "latest release"

Vitess maintains **parallel stable release trains** (e.g. `v24.0.x` and `v23.0.x`, with security backports reaching older trains such as `v22.0.x`). "Latest upstream" is the newest tag of **every** maintained train, not the newest GitHub release overall.

- Group upstream releases by series (`vNN.0.x`) and add an entry for every series with an untracked release — a security backport on an old train can be the newest release overall (e.g. `v22.0.4`).
- Release candidates (`vNN.0.0-rcN`) are published GitHub releases and are tracked as regular entries.
- The 10-entry cap is across all trains combined, newest first; series interleave.
- A train is dropped only when upstream clearly stops servicing it (no new tags and no new `changelog/<train>/` directory); existing entries for a dropped train are never deleted.

### Changelog links

Every `releases.md` entry links the GitHub release page, plus the in-repo changelog at `changelog/<train>/<tag>/changelog.md` when it exists (RCs and some security releases have none).

### Versioned docs

All doc links in this folder point at the currently tracked train, `https://vitess.io/docs/<train>/` (currently `24.0`). When a new train reaches GA and becomes the default at `https://vitess.io/docs/`:

- Bump the train in every doc link in the four standard documents and the `features/` detail files, in the same commit.
- Update the "vNN.0 docs" line in this README.
- Record the new train's headline features in `features.md` with a "new in vNN" note.

Feature statements that depend on a specific version (flags, behavior changes, removals) cite that version and link to the train's docs, not to unversioned paths.

### API surface

`api-reference.md` lists control-plane surfaces (protocol, daemons, CLI), not Kinds. Check for changes against the upstream program reference (`reference/programs/`) and the gRPC service definitions in `go/vt/proto/` (VTGate, vtctld, vtadmin, vtorc, VStream). Add or remove rows only when a surface is added, removed, or renamed; the MySQL wire-protocol row is stable unless the protocol itself changes.

### The `features/` subfolder

`features.md` is the index (one line per feature area: upstream doc link + detail note); extended per-feature notes live in `features/<feature>.md`, each with its own frontmatter. When a run updates a feature area:

- Update the `features.md` line **and** the corresponding `features/<feature>.md`; bump `last_updated` on every file touched.
- A new feature area gets both an index line and a detail file.
- Detail files cite Go source paths in `main`; when re-verifying, confirm the cited paths still exist upstream and fix moved or removed links.

### Boundary with the Vitess Operator

`kubernetes/vitess-operator` (upstream `planetscale/vitess-operator`) is a **separately tracked project**. Operator CRDs, releases, and features belong in that folder, not here: the operator entries in this folder are pointers only. Keep the cross-link to [kubernetes/vitess-operator](../../kubernetes/vitess-operator/README.md) accurate and do not duplicate its content.
