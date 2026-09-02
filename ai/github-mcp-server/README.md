---
upstream: https://github.com/github/github-mcp-server
last_updated: 2026-08-22
---

# github-mcp-server

The GitHub MCP Server is GitHub's official [Model Context Protocol (MCP)](https://modelcontextprotocol.io/) server. It connects AI tools — agents, assistants, chatbots — directly to the GitHub platform: reading repositories and code, managing issues and pull requests, monitoring Actions/CI, analyzing code-security findings, and working with Projects, discussions, and notifications. That surface is exposed as a large catalog of typed MCP tools (87 in the auto-generated [catalog](https://github.com/github/github-mcp-server#tools), plus a few remote-only ones) grouped into configurable toolsets, so each integration can enable exactly the capabilities it needs. It ships as a single Go binary and Docker image (`ghcr.io/github/github-mcp-server`) with stdio and HTTP transports, and is also hosted by GitHub as a remote server.

- Upstream repository: https://github.com/github/github-mcp-server
- Documentation: in-repo [docs/](https://github.com/github/github-mcp-server/tree/main/docs) and the [README](https://github.com/github/github-mcp-server#readme)
- Remote server: https://api.githubcopilot.com/mcp/
- Docker image: `ghcr.io/github/github-mcp-server`
- License: [MIT](https://github.com/github/github-mcp-server/blob/main/LICENSE)

## Homelab deployment

Active in the homelab cluster via the `mmontes11/k8s-ai` repository (`infrastructure/mcp-github`): a `Deployment` running `ghcr.io/github/github-mcp-server:1.0.4` on the HTTP transport (port 8080) with `--toolsets default,stargazers`; the GitHub token is injected from the `gh-token` secret by name. Note the pinned image predates every release in [releases.md](releases.md).

## Standard documents

- [API reference](api-reference.md)
- [Releases](releases.md)
- [Features](features.md)
