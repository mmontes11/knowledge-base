# ai

Projects in the AI domain.

| Project | Description |
| ------- | ----------- |
| [kubernetes-mcp-server](kubernetes-mcp-server/) | Go-native Model Context Protocol server for Kubernetes and OpenShift that exposes cluster operations (generic resource CRUD, pods, events, nodes, Helm, plus optional toolsets for KubeVirt, Kiali, Tekton, NetObserv, and kcp) as MCP tools, prompts, and configuration, with multi-cluster support, read-only/destructive-safe modes, OIDC authentication, and OTel observability. |
Projects in the AI / LLM tooling domain.
| [mcp-grafana](mcp-grafana/) | Model Context Protocol (MCP) server for Grafana: exposes dashboards, datasources, and the surrounding observability ecosystem (Prometheus, Loki, Pyroscope, Alerting, OnCall, Sift, Incident, Annotations, Snapshots, Rendering, and more) as a large set of typed MCP tools that LLM clients such as Claude Desktop, Cursor, and VS Code can call over `stdio`, `sse`, or `streamable-http`. |
Projects in the AI domain.
| [github-mcp-server](github-mcp-server/) | GitHub's official Model Context Protocol (MCP) server that connects AI agents and assistants directly to the GitHub platform — repositories and code, issues, pull requests, Actions/CI, code security, Projects, and teams — as a catalog of typed MCP tools grouped into toolsets, delivered over stdio or HTTP. |
Projects in the AI / machine-learning domain: workloads, schedulers, and operators that run ML inference and training on Kubernetes.
| [lws](lws/) | Kubernetes API (LeaderWorkerSet) that deploys a leader pod plus N worker pods as a single unit of replication for distributed workloads (LLM inference, train/serve stacks), with gang-aware rollout, restart policies, and multi-role disaggregated inference (DisaggregatedSet) plus per-role autoscaling. |
Projects in the AI / machine-learning serving domain.
| [kserve](kserve/) | CNCF-incubating, Kubernetes-native platform for deploying and scaling traditional ML models and LLMs: a single `InferenceService` API covering standard (Knative serverless, scale-to-zero, canary) and raw (`Deployment`) modes, a multi-framework serving-runtime ecosystem (TensorFlow, PyTorch, ONNX, XGBoost, scikit-learn, Hugging Face, vLLM), the Gateway-API-based `LLMInferenceService` for LLM inference pooling, local model caching, and multi-node / multi-GPU serving. |
Projects in the AI / local-inference domain.
| [llama-cpp](llama-cpp/) | Plain C/C++ LLM and VLM inference engine built on the ggml tensor library: GGUF quantized models (1.5–8-bit), an OpenAI-compatible HTTP server with built-in web UI, and hardware backends from Apple Silicon Metal to CUDA, Vulkan, SYCL, and OpenVINO. |
Projects in the AI domain.
| [n8n](n8n/) | Fair-code (Sustainable Use License) workflow automation platform with native AI capabilities: a visual canvas with 400+ built-in integration nodes, triggers (webhook, schedule, chat, MCP server), a LangChain-based AI Agent node with MCP tools, an AI Assistant that edits workflows by chat, a REST API + CLI, built-in data tables, RBAC, and queue-mode horizontal scaling. Deploy with Docker or Helm (OCI chart `oci://ghcr.io/n8n-io/n8n-helm-chart/n8n`). |
Projects in the AI / machine-learning domain.
| [comfyui](comfyui/) | Modular diffusion-model GUI, API and backend built around a visual node graph: composes image, video, audio, 3D, and text generation workflows without code, with an async queue, smart VRAM/RAM management, broad native model support, custom nodes, and a local HTTP API for production pipelines. |
Projects in the AI application and platform domain.
| [open-webui](open-webui/) | Extensible, self-hosted AI frontend platform: a SvelteKit web app over a FastAPI backend that fronts Ollama and OpenAI-compatible model providers, with chat features (multi-model conversations, web search, image generation, voice), RAG knowledge bases over multiple vector DBs, tools/function calling, multi-user RBAC, and a Python plugin pipeline (filters/actions/pipes/tools/skills) with MCP and OpenAPI server support. |
Projects in the AI domain.
| [multica](multica/) | Open-source workspace for assigning work to AI coding agents like teammates: agents pick up issues, run on your runtimes (23 supported CLIs), report progress, and hand work back for review. Self-hostable via Docker Compose or Helm. |
| [vllm](vllm/) | High-throughput, memory-efficient inference and serving engine for LLMs: 200+ Hugging Face models behind an OpenAI-compatible server, continuous batching with PagedAttention KV-cache management, quantization (FP8, MXFP4, NVFP4, …), tensor/pipeline/data/expert/context parallelism, speculative decoding, multi-LoRA, and disaggregated prefill/decode or KV offloading. |
| [opencode](opencode/) | Open-source AI coding agent with a client/server core: TUI, desktop, and web clients around one documented HTTP/SSE agent server; multi-provider, MCP/LSP/ACP, plugins, and a scriptable CLI/SDK. MIT, npm `opencode-ai`. |
