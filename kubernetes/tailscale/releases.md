---
upstream: https://github.com/tailscale/tailscale
last_updated: 2026-08-16
---

# tailscale — releases

Latest 10 official releases of the `tailscale/tailscale` project (the upstream for the Kubernetes operator), newest first. Scan the ⚠️ entries before upgrading. The Kubernetes operator itself ships separately as the `tailscale-operator` Helm chart; the version deployed in this repository is `1.96.5`.

## v1.102.2 — 2026-08-04
[Release page](https://github.com/tailscale/tailscale/releases/tag/v1.102.2)
- Fix a regression that broke **incoming Tailscale Funnel** connections.

## v1.102.1 — 2026-08-03
[Release page](https://github.com/tailscale/tailscale/releases/tag/v1.102.1)
- **Serve/Funnel**: client metrics now report `bytes`; Funnel TLS-ALPN-01 domains; proactive TLS auto-renewal and parallel certificate issuance.
- **New CLI**: `tailscale get`, `tailscale whoami`, and `tailscale service list`; node add/remove on large tailnets is now O(1).
- ⚠️ **Removed the deprecated `4via6` MagicDNS name format.**
- **Security**: `TS-2026-010` SSH environment-variable leak fix; fixed a WireGuard handshake memory leak.

## v1.98.10 — 2026-07-28
[Release page](https://github.com/tailscale/tailscale/releases/tag/v1.98.10)
- **Security**: `TS-2026-004` hardens SSH Unix-socket symlink permissions; `TS-2026-006` further restricts UIDs / numeric-only usernames.

## v1.98.9 — 2026-07-20
[Release page](https://github.com/tailscale/tailscale/releases/tag/v1.98.9)
- ⚠️ **Multiple security fixes**: `TS-2026-005` (Serve Unix-socket proxy restricted to root), `TS-2026-006` (SSH UID handling), `TS-2026-007` (service node rejects non-advertised ports), `TS-2026-008` (Serve/Funnel path-traversal fix), `TS-2026-009` (forbids leading-dash usernames).

## v1.98.8 — 2026-06-30
[Release page](https://github.com/tailscale/tailscale/releases/tag/v1.98.8)
- **wireguard-go fixes**: sleep/wake disconnection, excessive handshake retries, and an SSH session-recordings connection leak.

## v1.98.5 — 2026-06-02
[Release page](https://github.com/tailscale/tailscale/releases/tag/v1.98.5)
- **macOS and iOS clients** are now built with the Xcode 26.5 toolchain.

## v1.98.3 — 2026-05-21
[Release page](https://github.com/tailscale/tailscale/releases/tag/v1.98.3)
- **Linux**: fixed a netfilter mode-change inconsistency.

## v1.98.2 — 2026-05-18
[Release page](https://github.com/tailscale/tailscale/releases/tag/v1.98.2)
- **Dependency**: Go `1.26.3`.
- **Fix**: 1.98.0 MagicDNS regression after network changes (Windows unaffected).

## v1.96.4 — 2026-03-27
[Release page](https://github.com/tailscale/tailscale/releases/tag/v1.96.4)
- **Linux**: fork fallback-on-`ENOSYS` fix; **MIPS**: startup segfault fixed; **Android**: disconnect deadlock fixed; **Synology**: fork updated.

## v1.96.3 — 2026-03-19
[Release page](https://github.com/tailscale/tailscale/releases/tag/v1.96.3)
- **Windows**: fixed DNS resolution for the NRPT rule format.
