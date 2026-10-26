# Shivam: Internal Technical Assessment (26 Oct 2026)

> **INTERNAL.** Second, deeper review. Includes AI-use, originality and placement guidance inferred from repository evidence and tool output. Confirm in conversation before acting on it.

**Repo:** [Abstergo-47/DevOps-SRE](https://github.com/Abstergo-47/DevOps-SRE)  
**Email:** shivamsingh.work@proton.me

**Bottom line:** Moved off the empty template, but only a Dockerfile and a single Deployment with the wrong memory limit.

## Placement read

| | |
|---|---|
| **Tier** | **Early**: Too little working output to judge, or stalled. |
| **Effect on placement chances** | Neutral |
| **Checkpoints (of 24)** | 1 done, 4 partial, 19 missing |
| **AI-generation likelihood** | Low |
| **Originality** | Clear |
| **Batch / module** | DevOps July25 / AWS 2 (Raham Shaik) |
| **Tech YOE (sheet)** | 3.08 |
| **Contest final** | Passed |
| **Mock interview** | completed / Hire |
| **Calling feedback** | n/a |

**Since 19 Oct:** Restarted the repo with a new history (default branch `master`, 'Initial commit' on 19 Oct) and added a Dockerfile and three Kubernetes files on 22 Oct.

## Activity timeline

- **Own commits:** 2 across 2 active day(s): 10-19, 10-22
- **First / last:** 2026-10-19 17:07 / 2026-10-22 02:15
- **PR merges:** 0 | **bot commits:** 0 | **branches:** master
- **Largest single commit:** +271 lines in 4 files (2026-10-19, "Initial commit")

<details><summary>Recent commits (newest last)</summary>

- `afbdbfb` 10-19 17:07 +271/-0 Initial commit
- `07fc0f0` 10-22 02:15 +63/-0 added docker and kubernetes files

</details>

## Technical deep dive

### Dockerfile
- python:3.14-slim, `useradd appuser` with no fixed UID, `USER` before `COPY --chown`. No HEALTHCHECK.

### Kubernetes
- One Deployment (`python-app:latest`, local image), memory limit **1Gi** instead of 400Mi, startup tcpSocket and liveness httpGet probes, `runAsUser: 1000` without `runAsNonRoot`.
- A namespace-wide default-deny-ingress NetworkPolicy with no allow rule and no Service, so nothing can reach the pod.

### CI/CD
- None.

## Automated tool results

Run on the default branch. hadolint, actionlint, kubeconform (`-strict`), kube-linter, kustomize, promtool, shellcheck, gitleaks over full git history, and checkov. 'Replay' runs the tests from the repo root.

| Tool | Target | Result |
|---|---|---|
| hadolint | `Dockerfile` | clean |
| kubeconform | `all manifests` | 3 resources found in 3 files - Valid: 3, Invalid: 0, Errors: 0, Skipped: 0 |
| kube-linter | `all manifests` | latest-tag x1, liveness-port x1, no-read-only-root-fs x1, startup-port x1 |
| gitleaks (full history) | `repo` | no leaks |
| checkov | `Dockerfile, K8s, GHA` | 11 failed checks (dockerfile 1, kubernetes 10) |
| pytest replay | `repo root` | 6 passed in 0.70s |

## Checkpoint scorecard

**Task 1: Docker and secure CI/CD**: Dockerfile (minimal base, non-root user) 🟢 · Branch protection (PR-only changes to main) 🔴 · PR pipeline: build the image 🔴 · PR pipeline: lint (Flake8) 🔴 · PR pipeline: SAST (B104 ignored) 🔴 · PR pipeline: secret scanning (optional) 🔴 · PR pipeline: unit tests (pytest) 🔴 · PR pipeline: image vulnerability scan 🔴 · Release pipeline: build and push to a registry 🔴 · Release pipeline: auto-update manifest image tags 🔴

**Task 2: GitOps with ArgoCD**: ArgoCD installed and syncing the repo 🔴 · Three Deployments behind one shared Service 🟡 · Requests set and memory limit of 400Mi 🟡 · Namespaces, ConfigMaps and Secrets 🟡 · initContainers 🔴 · RBAC (ServiceAccount, Role, RoleBinding) 🔴 · NetworkPolicies 🟡 · ResourceQuotas 🔴

**Task 3: Observability and alerting**: Prometheus and Grafana via Helm 🔴 · Dashboard: node and per-pod CPU/memory 🔴 · Log aggregation (Loki, EFK or similar) 🔴 · Grafana alerts: health check and OOMKilled 🔴 · Alert routing to Slack or email (bonus) 🔴

**Documentation**: Setup, architecture and reproduction steps 🔴

## AI usage and originality evidence

| Signal | Where | What we saw | Points to |
|---|---|---|---|
| Small manual changes | `git log` | Two commits, simple files. | Against AI |

**AI read:** Hands-on, small.

**Originality:** No overlap.

## Verification questions for a screening call

1. Why 1Gi?
2. How does traffic reach your pod with a deny-all policy and no Service?

## What would move them up

- PR pipeline.
- 400Mi limit, three Deployments and a Service.
