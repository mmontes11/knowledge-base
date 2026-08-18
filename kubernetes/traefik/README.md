---
upstream: https://github.com/traefik/traefik
last_updated: 2026-08-17
---

# traefik

[Traefik](https://traefik.io/) is a modern, self-updating, and reverse proxy / L7 load balancer. On Kubernetes it runs as an ingress controller: it watches `Ingress`, its own CRDs (`traefik.io`), and [Gateway API](https://gateway-api.sigs.k8s.io/) resources, and turns them into dynamic routing configuration (routers, services, middlewares, TLS) that is pushed into the running proxy without restarts. A single instance exposes one or more entrypoints (e.g. `web`/`websecure`), and routing, termination, load balancing, and middleware behavior are all data-driven from the resources it watches.

- Upstream repository: https://github.com/traefik/traefik
- Documentation: https://doc.traefik.io/traefik/ (v3.7 is the documented line used by the links in this folder)
- Helm chart: https://github.com/traefik/helm-chart (also published as `oci://ghcr.io/traefik/helm/traefik`)
- License: MIT

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
