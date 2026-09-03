---
upstream: https://github.com/tailscale/tailscale
last_updated: 2026-09-03
---

# tailscale — releases

Latest 10 official releases of the `tailscale/tailscale` project (the upstream for the Kubernetes operator), newest first. Scan the ⚠️ entries before upgrading. The Kubernetes operator itself ships separately as the `tailscale-operator` Helm chart; the version deployed in this repository is `1.96.5`.

## v1.102.3 — 2026-08-20
[Release page](https://github.com/tailscale/tailscale/releases/tag/v1.102.3)
- **Kubernetes operator**: [peer relays](https://tailscale.com/kb/1370/kubernetes-operator-peer-relay) on AWS are now reachable regardless of the pod's availability zone (pinning Elastic IPs also requires pinning pods to the same zone via a `ProxyClass`); NLBs fronting peer relays use HTTP instead of TCP for pod health checks, fixing every target being reported unhealthy.
- **Security**: `TS-2026-011` — Tailscale refuses host-scoped IPv4 destinations at every point acting on an unmapped 4via6 address.
- **Client**: Go updated to `1.26.6`; unqualified hostnames are correctly forwarded to configured nameservers when MagicDNS is disabled; Tailnet Lock startup failures on large tailnets fixed; Windows installer and `tailscaled` IPv6/NetBIOS fixes; reduced iOS/tvOS memory usage on large tailnets.
- **Container image**: new `TS_BOOT_TIMEOUT` env var controls how long the container waits for its initial connection (duration, default `60s`).

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
