# Ignatius Nobel Chittilappilly: Internal Technical Assessment (26 Oct 2026)

> **INTERNAL.** Second, deeper review. Includes AI-use, originality and placement guidance inferred from repository evidence and tool output. Confirm in conversation before acting on it.

**Repo:** [Ignatius-Nobel/Devops-SRE](https://github.com/Ignatius-Nobel/Devops-SRE)  
**Email:** csnobel2001@gmail.com

**Bottom line:** A correct slim Dockerfile, nothing else.

## Placement read

| | |
|---|---|
| **Tier** | **Early**: Too little working output to judge, or stalled. |
| **Effect on placement chances** | Neutral |
| **Checkpoints (of 24)** | 1 done, 0 partial, 23 missing |
| **AI-generation likelihood** | Low |
| **Originality** | Low-confidence similarity (Utkarsh) |
| **Batch / module** | DevOps Jan26 / DevOps Tools 2 (Rahul Chaubey) |
| **Tech YOE (sheet)** | 1.83 |
| **Contest final** | Passed |
| **Mock interview** | completed / Reject |
| **Calling feedback** | DNP |

**Since 19 Oct:** Moved off starter-only: Dockerfile added 21 Oct.

## Activity timeline

- **Own commits:** 2 across 2 active day(s): 10-18, 10-21
- **First / last:** 2026-10-18 20:26 / 2026-10-21 22:48
- **PR merges:** 0 | **bot commits:** 0 | **branches:** main
- **Largest single commit:** +251 lines in 3 files (2026-10-18, "initial commit")

<details><summary>Recent commits (newest last)</summary>

- `e5dfbed` 10-18 20:26 +251/-0 initial commit
- `363aeb3` 10-21 22:48 +39/-0 added Dockerfile

</details>

## Technical deep dive

### Dockerfile
- python:3.12-slim, system user with home, HEALTHCHECK, pip no-cache env. hadolint clean.

### Everything else
- Not started.

## Automated tool results

Run on the default branch. hadolint, actionlint, kubeconform (`-strict`), kube-linter, kustomize, promtool, shellcheck, gitleaks over full git history, and checkov. 'Replay' runs the tests from the repo root.

| Tool | Target | Result |
|---|---|---|
| hadolint | `Dockerfile` | clean |
| gitleaks (full history) | `repo` | no leaks |
| checkov | `Dockerfile, K8s, GHA` | 0 failed checks |
| pytest replay | `repo root` | 6 passed in 0.62s |

## Checkpoint scorecard

**Task 1: Docker and secure CI/CD**: Dockerfile (minimal base, non-root user) 🟢 · Branch protection (PR-only changes to main) 🔴 · PR pipeline: build the image 🔴 · PR pipeline: lint (Flake8) 🔴 · PR pipeline: SAST (B104 ignored) 🔴 · PR pipeline: secret scanning (optional) 🔴 · PR pipeline: unit tests (pytest) 🔴 · PR pipeline: image vulnerability scan 🔴 · Release pipeline: build and push to a registry 🔴 · Release pipeline: auto-update manifest image tags 🔴

**Task 2: GitOps with ArgoCD**: ArgoCD installed and syncing the repo 🔴 · Three Deployments behind one shared Service 🔴 · Requests set and memory limit of 400Mi 🔴 · Namespaces, ConfigMaps and Secrets 🔴 · initContainers 🔴 · RBAC (ServiceAccount, Role, RoleBinding) 🔴 · NetworkPolicies 🔴 · ResourceQuotas 🔴

**Task 3: Observability and alerting**: Prometheus and Grafana via Helm 🔴 · Dashboard: node and per-pod CPU/memory 🔴 · Log aggregation (Loki, EFK or similar) 🔴 · Grafana alerts: health check and OOMKilled 🔴 · Alert routing to Slack or email (bonus) 🔴

**Documentation**: Setup, architecture and reproduction steps 🔴

## AI usage and originality evidence

| Signal | Where | What we saw | Points to |
|---|---|---|---|
| Matches another learner's lines | `Dockerfile` | The groupadd/useradd line and HEALTHCHECK command are character-identical to Utkarsh's. Different batches, so a common AI output is more likely than copying. | Supports AI (weak) |

**AI read:** Possibly AI-generated, but too small to matter.

**Originality:** Low-confidence similarity with Utkarsh's Dockerfile.

## Verification questions for a screening call

1. Explain each line of your Dockerfile.

## What would move them up

- PR pipeline.
