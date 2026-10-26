# utkarsh: Internal Technical Assessment (26 Oct 2026)

> **INTERNAL.** Second, deeper review. Includes AI-use, originality and placement guidance inferred from repository evidence and tool output. Confirm in conversation before acting on it.

**Repo:** [utkarshpopli26/DevOps-SRE](https://github.com/utkarshpopli26/DevOps-SRE)  
**Email:** utkarsh.popli26@gmail.com

**Bottom line:** Unchanged single 814-line drop, docs that overclaim, and a PR pipeline that still can't pass.

## Placement read

| | |
|---|---|
| **Tier** | **Hold**: Likely AI-generated; verify live before any credit. |
| **Effect on placement chances** | Decrease until verified |
| **Checkpoints (of 24)** | 12 done, 7 partial, 5 missing |
| **AI-generation likelihood** | High |
| **Originality** | Clear |
| **Batch / module** | DevOps Nov25 / DevOps Tools 2 (Akshat Gupta) |
| **Tech YOE (sheet)** | 3.25 |
| **Contest final** | Passed |
| **Mock interview** | completed / Maybe |
| **Calling feedback** | DNP |

**Since 19 Oct:** No commits since 17 Oct.

## Activity timeline

- **Own commits:** 1 across 1 active day(s): 10-17
- **First / last:** 2026-10-17 18:20 / 2026-10-17 18:20
- **PR merges:** 0 | **bot commits:** 1 | **branches:** main
- **Largest single commit:** +814 lines in 16 files (2026-10-17, "Containerize, CICD, Kubernetes setup")

<details><summary>Recent commits (newest last)</summary>

- `a6d8fe2` 10-17 18:20 +814/-59 Containerize, CICD, Kubernetes setup

</details>

## Technical deep dive

### PR pipeline
- `bandit -x B104` (exclude path) instead of `-s B104`, so Bandit fails. Flake8 fails on the starter. Has never run.

### Release
- Placeholder-based tag script works exactly once. All three tags point at one image.

### Kubernetes
- kubeconform strict 14/14, kube-linter run-as-non-root and no-read-only-root-fs on four containers. The NetworkPolicy admits only app-to-app traffic, which blocks monitoring and any external client. A plaintext Secret is committed (gitleaks).

## Automated tool results

Run on the default branch. hadolint, actionlint, kubeconform (`-strict`), kube-linter, kustomize, promtool, shellcheck, gitleaks over full git history, and checkov. 'Replay' runs the tests from the repo root.

| Tool | Target | Result |
|---|---|---|
| hadolint | `Dockerfile` | clean |
| actionlint | `.github/workflows/pr-ci.yml` | clean |
| actionlint | `.github/workflows/release.yml` | clean |
| kubeconform | `all manifests` | 15 resources found in 7 files - Valid: 14, Invalid: 0, Errors: 0, Skipped: 1 |
| kube-linter | `all manifests` | no-read-only-root-fs x4, run-as-non-root x4, unset-memory-requirements x3, no-anti-affinity x2, unset-cpu-requirements x2 |
| gitleaks (full history) | `kubernetes/configmap-secret.yaml` | 1 finding(s): kubernetes-secret-yaml |
| checkov | `Dockerfile, K8s, GHA` | 51 failed checks (kubernetes 51) |
| pytest replay | `repo root` | 6 passed in 0.62s |

## Checkpoint scorecard

**Task 1: Docker and secure CI/CD**: Dockerfile (minimal base, non-root user) 🟢 · Branch protection (PR-only changes to main) 🟡 · PR pipeline: build the image 🟡 · PR pipeline: lint (Flake8) 🟡 · PR pipeline: SAST (B104 ignored) 🟡 · PR pipeline: secret scanning (optional) 🟢 · PR pipeline: unit tests (pytest) 🟢 · PR pipeline: image vulnerability scan 🟢 · Release pipeline: build and push to a registry 🟢 · Release pipeline: auto-update manifest image tags 🟡

**Task 2: GitOps with ArgoCD**: ArgoCD installed and syncing the repo 🟡 · Three Deployments behind one shared Service 🟢 · Requests set and memory limit of 400Mi 🟢 · Namespaces, ConfigMaps and Secrets 🟢 · initContainers 🟢 · RBAC (ServiceAccount, Role, RoleBinding) 🟢 · NetworkPolicies 🟡 · ResourceQuotas 🟢

**Task 3: Observability and alerting**: Prometheus and Grafana via Helm 🔴 · Dashboard: node and per-pod CPU/memory 🔴 · Log aggregation (Loki, EFK or similar) 🔴 · Grafana alerts: health check and OOMKilled 🔴 · Alert routing to Slack or email (bonus) 🔴

**Documentation**: Setup, architecture and reproduction steps 🟢

## AI usage and originality evidence

| Signal | Where | What we saw | Points to |
|---|---|---|---|
| Single drop, no iteration | `a6d8fe2` | 814 lines, one commit. | Supports AI |
| False claims | `readme.md` | States branch protection blocks pushes; it doesn't. | Supports AI |

**AI read:** Unchanged: likely generated, never run.

**Originality:** No overlap. His Dockerfile user-creation and HEALTHCHECK lines also appear verbatim in Nobel's repo; likely a shared AI output.

## Verification questions for a screening call

1. bandit -x vs -s?
2. Show me the branch protection rule your README describes.

## What would move them up

- Live walkthrough.
