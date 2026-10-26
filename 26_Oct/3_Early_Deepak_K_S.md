# Deepak K S: Internal Technical Assessment (26 Oct 2026)

> **INTERNAL.** Second, deeper review. Includes AI-use, originality and placement guidance inferred from repository evidence and tool output. Confirm in conversation before acting on it.

**Repo:** [Deepak-k-s/devops-project1](https://github.com/Deepak-k-s/devops-project1)  
**Email:** deepakshivakumar07@gmail.com

**Bottom line:** Good habits (SHA-pinned actions, branch and PR flow) but no progress in eight days.

## Placement read

| | |
|---|---|
| **Tier** | **Early**: Too little working output to judge, or stalled. |
| **Effect on placement chances** | Slight decrease (stalled) |
| **Checkpoints (of 24)** | 5 done, 2 partial, 17 missing |
| **AI-generation likelihood** | Low |
| **Originality** | Clear |
| **Batch / module** | DevOps Sept25 / AWS 2 (Raham Shaik) |
| **Tech YOE (sheet)** | 1.83 |
| **Contest final** | Passed |
| **Mock interview** | No / n/a |
| **Calling feedback** | Not yet started. Will start this weekend. |

**Since 19 Oct:** No commits since 18 Oct. Work is still on unmerged branches, and PR #1 still fails on lint.

## Activity timeline

- **Own commits:** 7 across 1 active day(s): 10-18
- **First / last:** 2026-10-18 16:58 / 2026-10-18 17:56
- **PR merges:** 0 | **bot commits:** 0 | **branches:** main, task1-docker-ci, task1-docker-pr
- **Largest single commit:** +199 lines in 4 files (2026-10-18, " created Dockerfile and checked the app funtionality")

<details><summary>Recent commits (newest last)</summary>

- `a54a740` 10-18 16:58 +199/-0  created Dockerfile and checked the app funtionality
- `263c98b` 10-18 16:58 +199/-0  created Dockerfile and checked the app funtionality
- `b17fcf3` 10-18 17:43 +53/-0 Add pull request validation workflow
- `6bb88a4` 10-18 17:43 +53/-0 Add pull request validation workflow
- `2e0befe` 10-18 17:53 +0/-199 Revert " created Dockerfile and checked the app funtionality"
- `a01d826` 10-18 17:55 +2/-0 Initial commit
- `9cc542f` 10-18 17:56 +1/-1 Fix capitalization in README description

</details>

## Technical deep dive

### Dockerfile (branch)
- python:3.12-slim, UID 10001, .dockerignore. hadolint clean.

### PR pipeline (branch)
- Flake8, Bandit `-s B104`, pytest, build, Trivy gating, with every action pinned to a commit SHA. Flake8 fails on the unmodified starter files, which is why PR #1 is red.

## Automated tool results

Run on the default branch (`task1-docker-pr` for this learner, since main is README only). hadolint, actionlint, kubeconform (`-strict`), kube-linter, kustomize, promtool, shellcheck, gitleaks over full git history, and checkov. 'Replay' runs the tests from the repo root.

| Tool | Target | Result |
|---|---|---|
| hadolint | `Dockerfile` | clean |
| actionlint | `.github/workflows/pr-checks.yml` | clean |
| gitleaks (full history) | `repo` | no leaks |
| checkov | `Dockerfile, K8s, GHA` | 1 failed checks (dockerfile 1) |
| pytest replay | `repo root` | 6 passed in 0.56s |

## Checkpoint scorecard

**Task 1: Docker and secure CI/CD**: Dockerfile (minimal base, non-root user) 🟢 · Branch protection (PR-only changes to main) 🟡 · PR pipeline: build the image 🟢 · PR pipeline: lint (Flake8) 🟡 · PR pipeline: SAST (B104 ignored) 🟢 · PR pipeline: secret scanning (optional) 🔴 · PR pipeline: unit tests (pytest) 🟢 · PR pipeline: image vulnerability scan 🟢 · Release pipeline: build and push to a registry 🔴 · Release pipeline: auto-update manifest image tags 🔴

**Task 2: GitOps with ArgoCD**: ArgoCD installed and syncing the repo 🔴 · Three Deployments behind one shared Service 🔴 · Requests set and memory limit of 400Mi 🔴 · Namespaces, ConfigMaps and Secrets 🔴 · initContainers 🔴 · RBAC (ServiceAccount, Role, RoleBinding) 🔴 · NetworkPolicies 🔴 · ResourceQuotas 🔴

**Task 3: Observability and alerting**: Prometheus and Grafana via Helm 🔴 · Dashboard: node and per-pod CPU/memory 🔴 · Log aggregation (Loki, EFK or similar) 🔴 · Grafana alerts: health check and OOMKilled 🔴 · Alert routing to Slack or email (bonus) 🔴

**Documentation**: Setup, architecture and reproduction steps 🔴

## AI usage and originality evidence

| Signal | Where | What we saw | Points to |
|---|---|---|---|
| Human commit text | `git log` | 'checked the app funtionality', a revert of an accidental main push. | Against AI |

**AI read:** Hands-on.

**Originality:** No overlap.

## Verification questions for a screening call

1. What is stopping you from fixing the Flake8 errors?
2. Why pin actions to SHAs?

## What would move them up

- Get PR #1 green and merged.
- Release pipeline.
