---
upstream: https://github.com/tailscale/tailscale
last_updated: 2026-08-16
---

# tailscale

[Tailscale](https://tailscale.com/) is a zero-trust mesh VPN (built on WireGuard) that places devices from any network, OS, or cloud into a single private, encrypted network (a "tailnet"). Tailscale's Kubernetes operator extends that to Kubernetes: instead of running Tailscale ad-hoc in each pod or node, the operator runs the Tailscale devices for you and keeps them in sync — joining nodes and workloads to the tailnet and managing [subnet routers and exit nodes](https://tailscale.com/kb/1441/kubernetes-operator-connector), [egress](https://tailscale.com/kb/1438/kubernetes-operator-cluster-egress) and [ingress](https://tailscale.com/kb/1439/kubernetes-operator-cluster-ingress) proxies, a kube-apiserver proxy, custom DNS, and SSH session recording, all driven by a small set of custom resources.

- Upstream repository: [tailscale/tailscale](https://github.com/tailscale/tailscale)
- Kubernetes operator documentation: [https://tailscale.com/kb/1236/kubernetes-operator](https://tailscale.com/kb/1236/kubernetes-operator)
- Customization reference (proxy tuning, DNS, multi-tailnet): [https://tailscale.com/kb/1445/kubernetes-operator-customization](https://tailscale.com/kb/1445/kubernetes-operator-customization)
- Helm chart: `tailscale-operator`, published at [https://pkgs.tailscale.com/helmcharts](https://pkgs.tailscale.com/helmcharts)
- License: BSD-3-Clause
- API group/version: `tailscale.com/v1alpha1`

This entry documents the API surface and feature set of the operator as deployed by `mmontes11/k8s-infrastructure` in `infrastructure/tailscale/` (the `tailscale-operator` Helm chart at version `1.96.5`).

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
