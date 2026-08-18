---
upstream: https://github.com/tailscale/tailscale
last_updated: 2026-08-16
---

# tailscale — features

Feature areas of the Tailscale Kubernetes operator, each linked to the upstream documentation that covers it. The Kubernetes operator documentation at [kb/1236](https://tailscale.com/kb/1236/kubernetes-operator) is the authoritative source.

## Tailnet connectivity

- **Subnet routers** (`Connector`) — a `Connector` device advertises an on-prem or cloud subnet to the tailnet so remote devices can reach it. [kb/1441](https://tailscale.com/kb/1441/kubernetes-operator-connector)
- **Exit nodes** (`Connector`) — a `Connector` with exit-node enabled lets tailnet devices route their non-tailnet (Internet) traffic through the node. In this repository `compute` and `compute-large` are deployed as exit nodes. [kb/1441](https://tailscale.com/kb/1441/kubernetes-operator-connector)
- **Multiple tailnets** (`Tailnet`) — manage more than one tailnet per operator, with per-tailnet OAuth credentials and control-plane login URLs. [kb/1445](https://tailscale.com/kb/1445/kubernetes-operator-customization)

## Egress and ingress proxies

- **Cluster egress** (`ProxyGroup` type `egress`) — a highly available set of egress proxies so workload traffic exits to the Internet from a fixed tailnet identity; one proxy group can serve many annotated Services. [kb/1438](https://tailscale.com/kb/1438/kubernetes-operator-cluster-egress)
- **Cluster ingress** (`ProxyGroup` type `ingress`) — expose cluster Services/Ingress to the tailnet (or the world, via Funnel) through stable proxy devices selected by the `tailscale.com/proxy-group` Service annotation. [kb/1439](https://tailscale.com/kb/1439/kubernetes-operator-cluster-ingress)
- **Per-namespace control** (`ProxyGroupPolicy`) — a namespaced allow-list restricting which proxy groups a namespace's Services may use for egress/ingress (empty list = none allowed). [kb/1445](https://tailscale.com/kb/1445/kubernetes-operator-customization)

## kube-apiserver proxy

- **kube-apiserver proxy** (`ProxyGroup` type `kube-apiserver`) — exposes the Kubernetes API server to the tailnet under a stable hostname, in `auth` (per-identity impersonation) or `noauth` mode; the proxy URL is reported in status. [kb/1445](https://tailscale.com/kb/1445/kubernetes-operator-customization)

## DNS

- **Custom DNS** (`DNSConfig`) — deploys a `ts.net` nameserver to resolve a subset of MagicDNS names, useful for egress/ingress proxy hostnames. [kb/1445](https://tailscale.com/kb/1445/kubernetes-operator-customization)

## Proxy customization

- **ProxyClasses** (`ProxyClass`) — reusable configuration profiles (resources, `nodeSelector`, tolerations, affinity, StatefulSet/Deployment options, `acceptRoutes`, …) applied to proxies and Connectors via `spec.proxyClass` or the `tailscale.com/proxy-class` label. [kb/1445 (ProxyClass)](https://tailscale.com/kb/1445/kubernetes-operator-customization#cluster-resource-customization-using-proxyclass-custom-resource)

## SSH

- **SSH session recording** (`Recorder`) — deploys a tsrecorder that records Tailscale SSH sessions to a local ephemeral volume or an S3-compatible endpoint. [kb/1484](https://tailscale.com/kb/1484/kubernetes-operator-deploying-tsrecorder)

## Deployment

- **Helm / GitOps** — the operator ships as the `tailscale-operator` Helm chart from [https://pkgs.tailscale.com/helmcharts](https://pkgs.tailscale.com/helmcharts); this repository applies it through Flux as `HelmRelease/networking/tailscale-operator` with `installCRDs: true`. [kb/1236](https://tailscale.com/kb/1236/kubernetes-operator)

## Security

- **Tailnet ACLs and identity** — devices join a tailnet subject to Tailscale's ACLs and per-device identity and tags; proxies default to the `tag:k8s` tag (and the operator to `tag:k8s-operator`), which must be granted to the identities that run them. [kb/1236](https://tailscale.com/kb/1236/kubernetes-operator)
