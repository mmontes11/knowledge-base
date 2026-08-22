# ai

Projects in the AI / machine-learning domain: workloads, schedulers, and operators that run ML inference and training on Kubernetes.

| Project | Description |
| ------- | ----------- |
| [lws](lws/) | Kubernetes API (LeaderWorkerSet) that deploys a leader pod plus N worker pods as a single unit of replication for distributed workloads (LLM inference, train/serve stacks), with gang-aware rollout, restart policies, and multi-role disaggregated inference (DisaggregatedSet) plus per-role autoscaling. |
