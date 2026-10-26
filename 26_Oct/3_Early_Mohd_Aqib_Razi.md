# Mohd Aqib Razi: Internal Technical Assessment (26 Oct 2026)

> **INTERNAL.** Second, deeper review. Includes AI-use, originality and placement guidance inferred from repository evidence and tool output. Confirm in conversation before acting on it.

**Repo:** [AQIBRAZI/DevOps-SRE_Part1](https://github.com/AQIBRAZI/DevOps-SRE_Part1)  
**Email:** aqib.razi1@hotmail.com

**Bottom line:** A good Alpine Dockerfile with HEALTHCHECK and .dockerignore, and nothing else.

## Placement read

| | |
|---|---|
| **Tier** | **Early**: Too little working output to judge, or stalled. |
| **Effect on placement chances** | Neutral |
| **Checkpoints (of 24)** | 1 done, 0 partial, 23 missing |
| **AI-generation likelihood** | Low |
| **Originality** | Clear |
| **Batch / module** | DevOps Oct25 / DevOps Tools 2 (Akshat Gupta) |
| **Tech YOE (sheet)** | 13.08 |
| **Contest final** | Passed |
| **Mock interview** | completed / Reject |
| **Calling feedback** | Said will submit it by EOD |

**Since 19 Oct:** New to the review. A single commit on 22 Oct.

## Activity timeline

- **Own commits:** 1 across 1 active day(s): 10-22
- **First / last:** 2026-10-22 10:33 / 2026-10-22 10:33
- **PR merges:** 0 | **bot commits:** 0 | **branches:** main
- **Largest single commit:** +36 lines in 6 files (2026-10-22, "Add Dockerfile, linter configuration, and dependencies")

<details><summary>Recent commits (newest last)</summary>

- `a3cac4d` 10-22 10:33 +36/-0 Add Dockerfile, linter configuration, and dependencies

</details>

## Technical deep dive

### Dockerfile
- python:3.12-alpine, non-root, `COPY --chown`, HEALTHCHECK with a timeout, and a thorough .dockerignore (keys, .env).

### Hygiene
- Committed `__pycache__` .pyc files (cpython-314) even though the .dockerignore excludes them, so there's no .gitignore.

### Everything else
- Not started, despite 13 years of experience on the sheet.

## Automated tool results

Run on the default branch. hadolint, actionlint, kubeconform (`-strict`), kube-linter, kustomize, promtool, shellcheck, gitleaks over full git history, and checkov. 'Replay' runs the tests from the repo root.

| Tool | Target | Result |
|---|---|---|
| hadolint | `Dockerfile` | clean |
| gitleaks (full history) | `repo` | no leaks |
| checkov | `Dockerfile, K8s, GHA` | 0 failed checks |
| pytest replay | `repo root` | 6 passed in 0.70s |

## Checkpoint scorecard

**Task 1: Docker and secure CI/CD**: Dockerfile (minimal base, non-root user) 🟢 · Branch protection (PR-only changes to main) 🔴 · PR pipeline: build the image 🔴 · PR pipeline: lint (Flake8) 🔴 · PR pipeline: SAST (B104 ignored) 🔴 · PR pipeline: secret scanning (optional) 🔴 · PR pipeline: unit tests (pytest) 🔴 · PR pipeline: image vulnerability scan 🔴 · Release pipeline: build and push to a registry 🔴 · Release pipeline: auto-update manifest image tags 🔴

**Task 2: GitOps with ArgoCD**: ArgoCD installed and syncing the repo 🔴 · Three Deployments behind one shared Service 🔴 · Requests set and memory limit of 400Mi 🔴 · Namespaces, ConfigMaps and Secrets 🔴 · initContainers 🔴 · RBAC (ServiceAccount, Role, RoleBinding) 🔴 · NetworkPolicies 🔴 · ResourceQuotas 🔴

**Task 3: Observability and alerting**: Prometheus and Grafana via Helm 🔴 · Dashboard: node and per-pod CPU/memory 🔴 · Log aggregation (Loki, EFK or similar) 🔴 · Grafana alerts: health check and OOMKilled 🔴 · Alert routing to Slack or email (bonus) 🔴

**Documentation**: Setup, architecture and reproduction steps 🔴

## AI usage and originality evidence

| Signal | Where | What we saw | Points to |
|---|---|---|---|
| Single tidy commit | `a3cac4d` | Too little to judge. | Neutral |

**AI read:** Not enough work to assess.

**Originality:** No overlap.

## Verification questions for a screening call

1. With your experience, what is blocking Tasks 1b to 3?

## What would move them up

- PR pipeline and release pipeline.
