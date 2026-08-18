---
upstream: https://github.com/tailscale/tailscale
last_updated: 2026-08-16
---

# tailscale — API reference

The Tailscale operator registers **7 custom resource kinds** under API group/version **`tailscale.com/v1alpha1`**, delivered by the `tailscale-operator` Helm chart (deployed at version `1.96.5` in this repository, installing CRDs via `installCRDs: true`). Kubernetes operator documentation lives upstream at [kb/1236](https://tailscale.com/kb/1236/kubernetes-operator), and customization (proxy tuning, DNS, multi-tailnet) at [kb/1445](https://tailscale.com/kb/1445/kubernetes-operator-customization).

| Kind | Short name | Scope | Purpose | Upstream docs |
| --- | --- | --- | --- | --- |
| `Connector` | `cn` | Cluster | A Tailscale device that joins a node to the tailnet and acts as a subnet router and/or exit node (advertising a subnet and/or providing tailnet egress). | [kb/1441](https://tailscale.com/kb/1441/kubernetes-operator-connector) |
| `ProxyClass` | — | Cluster | A reusable configuration profile (resources, nodeSelector, tolerations, affinity, StatefulSet/Deployment settings, `acceptRoutes`, …) applied to proxies/Connectors via `spec.proxyClass` or the `tailscale.com/proxy-class` label. | [kb/1445 (ProxyClass)](https://tailscale.com/kb/1445/kubernetes-operator-customization#cluster-resource-customization-using-proxyclass-custom-resource) |
| `DNSConfig` | `dc` | Cluster | Deploys a `ts.net` nameserver for a subset of Tailscale MagicDNS names, for use with egress/ingress proxies. | [kb/1445](https://tailscale.com/kb/1445/kubernetes-operator-customization) |
| `Tailnet` | `tn` | Cluster | Multi-tailnet support: per-tailnet OAuth credentials (a Secret holding `client_id` / `client_secret`) and the control-plane login URL. | [kb/1445](https://tailscale.com/kb/1445/kubernetes-operator-customization) |
| `ProxyGroup` | `pg` | Cluster | A highly available group of egress, ingress, or kube-apiserver proxy devices so many annotated Services can share one proxy set; selected by the `tailscale.com/proxy-group` Service annotation. | [kb/1438](https://tailscale.com/kb/1438/kubernetes-operator-cluster-egress), [kb/1439](https://tailscale.com/kb/1439/kubernetes-operator-cluster-ingress) |
| `ProxyGroupPolicy` | `pgp` | Namespaced | A per-namespace allow-list restricting which `ProxyGroup` names may be used for egress/ingress in that namespace (an empty list allows none). | [kb/1445](https://tailscale.com/kb/1445/kubernetes-operator-customization) |
| `Recorder` | `rec` | Cluster | Deploys a tsrecorder for Tailscale SSH session recording, storing sessions to a local ephemeral volume or an S3-compatible endpoint. | [kb/1484](https://tailscale.com/kb/1484/kubernetes-operator-deploying-tsrecorder) |

## Notes

- All 7 kinds use `tailscale.com/v1alpha1` (the only served/stored version). Six are cluster-scoped; `ProxyGroupPolicy` is the only namespaced kind. Short names come from each CRD's `spec.names.shortNames`; `ProxyClass` defines none.
- `ProxyGroup.spec.type` is immutable and one of `egress`, `ingress`, or `kube-apiserver`; `Recorder` and the proxy kinds track their devices in `status` (e.g. `devices.hostname`, `url`, and per-kind `*Ready` / `*Available` conditions).
- In `mmontes11/k8s-infrastructure`, `infrastructure/tailscale/` defines `Connector/compute`, `Connector/compute-large`, `ProxyClass/compute`, and `ProxyClass/compute-large` (exit-node subnet routers on the `compute` / `compute-large` node pools). The operator is applied through Flux as `HelmRelease/networking/tailscale-operator` with both `installCRDs: true` and `crds: CreateReplace`.
- Field-level schema is intentionally not duplicated here; follow the per-kind upstream links above.
