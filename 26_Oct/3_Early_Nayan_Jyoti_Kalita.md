# Nayan Jyoti Kalita: Internal Technical Assessment (26 Oct 2026)

> **INTERNAL.** Second, deeper review. Includes AI-use, originality and placement guidance inferred from repository evidence and tool output. Confirm in conversation before acting on it.

**Repo:** [NayanJyotiKalita/DevSecOps-SRE-Project](https://github.com/NayanJyotiKalita/DevSecOps-SRE-Project)  
**Email:** nayanjyotikalita170@gmail.com

**Bottom line:** A verified, well-documented Dockerfile, then nothing for a week.

## Placement read

| | |
|---|---|
| **Tier** | **Early**: Too little working output to judge, or stalled. |
| **Effect on placement chances** | Slight decrease (stalled) |
| **Checkpoints (of 24)** | 1 done, 1 partial, 22 missing |
| **AI-generation likelihood** | Low |
| **Originality** | Clear |
| **Batch / module** | DevOps Aug24 3 / MLOps (Kashish Bhagat) |
| **Tech YOE (sheet)** | 2 |
| **Contest final** | Passed |
| **Mock interview** | No / n/a |
| **Calling feedback** | Not yet started. Will start this weekend. |

**Since 19 Oct:** No commits since 19 Oct.

## Activity timeline

- **Own commits:** 6 across 2 active day(s): 10-18, 10-19
- **First / last:** 2026-10-18 17:43 / 2026-10-19 00:37
- **PR merges:** 0 | **bot commits:** 0 | **branches:** main
- **Largest single commit:** +251 lines in 3 files (2026-10-18, "Fresh Commit")

<details><summary>Recent commits (newest last)</summary>

- `5c37f67` 10-18 17:43 +251/-0 Fresh Commit
- `b63bb59` 10-18 17:50 +0/-78 readme deleted
- `22b2a04` 10-18 19:06 +31/-0 Dockerfile and .dockerignore files are added
- `1bac4c1` 10-19 00:33 +100/-0 Document Dockerfile implementation and usage
- `4796126` 10-19 00:37 +0/-0 Rename README.md to README-documentation.md
- `4b111a9` 10-19 00:37 +0/-0 Rename README-documentation.md to README.md

</details>

## Technical deep dive

### Dockerfile
- python:3.12-alpine, fixed UID/GID, `COPY --chown`, .dockerignore, tested with `--read-only`. No HEALTHCHECK. hadolint clean.

### Everything else
- Not started.

## Automated tool results

Run on the default branch. hadolint, actionlint, kubeconform (`-strict`), kube-linter, kustomize, promtool, shellcheck, gitleaks over full git history, and checkov. 'Replay' runs the tests from the repo root.

| Tool | Target | Result |
|---|---|---|
| hadolint | `Dockerfile` | clean |
| gitleaks (full history) | `repo` | no leaks |
| checkov | `Dockerfile, K8s, GHA` | 1 failed checks (dockerfile 1) |
| pytest replay | `repo root` | 6 passed in 0.65s |

## Checkpoint scorecard

**Task 1: Docker and secure CI/CD**: Dockerfile (minimal base, non-root user) 🟢 · Branch protection (PR-only changes to main) 🔴 · PR pipeline: build the image 🔴 · PR pipeline: lint (Flake8) 🔴 · PR pipeline: SAST (B104 ignored) 🔴 · PR pipeline: secret scanning (optional) 🔴 · PR pipeline: unit tests (pytest) 🔴 · PR pipeline: image vulnerability scan 🔴 · Release pipeline: build and push to a registry 🔴 · Release pipeline: auto-update manifest image tags 🔴

**Task 2: GitOps with ArgoCD**: ArgoCD installed and syncing the repo 🔴 · Three Deployments behind one shared Service 🔴 · Requests set and memory limit of 400Mi 🔴 · Namespaces, ConfigMaps and Secrets 🔴 · initContainers 🔴 · RBAC (ServiceAccount, Role, RoleBinding) 🔴 · NetworkPolicies 🔴 · ResourceQuotas 🔴

**Task 3: Observability and alerting**: Prometheus and Grafana via Helm 🔴 · Dashboard: node and per-pod CPU/memory 🔴 · Log aggregation (Loki, EFK or similar) 🔴 · Grafana alerts: health check and OOMKilled 🔴 · Alert routing to Slack or email (bonus) 🔴

**Documentation**: Setup, architecture and reproduction steps 🟡

## AI usage and originality evidence

| Signal | Where | What we saw | Points to |
|---|---|---|---|
| Stock phrasing in README | `README.md` | 'hardened single-stage Alpine', 'living-off-the-land attacks'. | Supports AI (writing) |
| Real terminal output | `README.md` | Build log, container ID, curl output. | Against AI |

**AI read:** AI-polished writing over hands-on work.

**Originality:** No overlap.

## Verification questions for a screening call

1. What is your plan for the PR pipeline this week?

## What would move them up

- PR pipeline.
