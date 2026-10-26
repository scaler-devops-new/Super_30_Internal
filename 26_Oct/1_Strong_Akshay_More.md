# Akshay More: Internal Technical Assessment (26 Oct 2026)

> **INTERNAL.** Second, deeper review. Includes AI-use, originality and placement guidance inferred from repository evidence and tool output. Confirm in conversation before acting on it.

**Repo:** [akshay228437/DevOps-SRE](https://github.com/akshay228437/DevOps-SRE)  
**Email:** moreakshay.2013@gmail.com

**Bottom line:** Textbook Task 1 done through real PRs with every gate green, then stalled for nine days.

## Placement read

| | |
|---|---|
| **Tier** | **Strong**: Clear positive signal; put forward now (gaps noted). |
| **Effect on placement chances** | Neutral (no new work) |
| **Checkpoints (of 24)** | 7 done, 2 partial, 15 missing |
| **AI-generation likelihood** | Low |
| **Originality** | Clear |
| **Batch / module** | DevOps Oct25 / DevOps Tools 2 (Akshat Gupta) |
| **Tech YOE (sheet)** | 3.83 |
| **Contest final** | Passed |
| **Mock interview** | No / n/a |
| **Calling feedback** | Not yet started. Will start this weekend. |

**Since 19 Oct:** No commits since 17 Oct. Still the cleanest Task 1, but nothing new in nine days.

## Activity timeline

- **Own commits:** 14 across 1 active day(s): 10-17
- **First / last:** 2026-10-17 13:36 / 2026-10-17 19:00
- **PR merges:** 4 | **bot commits:** 0 | **branches:** feature/verify-ci, main
- **Largest single commit:** +197 lines in 3 files (2026-10-17, "pr-check")

<details><summary>Recent commits (newest last)</summary>

- `d98cfcf` 10-17 13:36 +14/-0 Dockerfile
- `2a934e7` 10-17 16:32 +197/-0 pr-check
- `24a2fcc` 10-17 16:43 +0/-2 Update README by removing documentation requirement
- `51460c8` 10-17 17:01 +38/-11 alpine
- `b4d1741` 10-17 17:22 +20/-20 linting fixes
- `366926b` 10-17 17:29 +1/-1 linting issue fixed
- `209a0dd` 10-17 18:35 +52/-0 release-yml
- `f3025a7` 10-17 18:36 +0/-0 Mrge branch 'feature/verify-ci' of https://github.com/akshay228437/DevOps-SRE into feature
- `6c69d8d` 10-17 18:58 +76/-72 task1 complete

</details>

## Technical deep dive

### Dockerfile
- python:3.12-alpine, UID 10001, no HEALTHCHECK (checkov CKV_DOCKER_2). hadolint clean.

### PR pipeline
- Flake8, pytest, CodeQL security-extended, and Trivy on HIGH/CRITICAL without ignore-unfixed (stricter than most). Job permissions scoped, concurrency and timeouts set.

### Release pipeline
- On merged PR, pushes `sha-<merge_sha>` to GHCR with persist-credentials off. No manifest update, because there are no manifests.

### Kubernetes and beyond
- Not started.

## Automated tool results

Run on the default branch. hadolint, actionlint, kubeconform (`-strict`), kube-linter, kustomize, promtool, shellcheck, gitleaks over full git history, and checkov. 'Replay' runs the tests from the repo root.

| Tool | Target | Result |
|---|---|---|
| hadolint | `Dockerfile` | clean |
| actionlint | `.github/workflows/pr-checks.yml` | clean |
| actionlint | `.github/workflows/release.yml` | clean |
| gitleaks (full history) | `repo` | no leaks |
| checkov | `Dockerfile, K8s, GHA` | 1 failed checks (dockerfile 1) |
| pytest replay | `repo root` | 6 passed in 0.63s |

## Checkpoint scorecard

**Task 1: Docker and secure CI/CD**: Dockerfile (minimal base, non-root user) 🟢 · Branch protection (PR-only changes to main) 🟡 · PR pipeline: build the image 🟢 · PR pipeline: lint (Flake8) 🟢 · PR pipeline: SAST (B104 ignored) 🟢 · PR pipeline: secret scanning (optional) 🔴 · PR pipeline: unit tests (pytest) 🟢 · PR pipeline: image vulnerability scan 🟢 · Release pipeline: build and push to a registry 🟢 · Release pipeline: auto-update manifest image tags 🔴

**Task 2: GitOps with ArgoCD**: ArgoCD installed and syncing the repo 🔴 · Three Deployments behind one shared Service 🔴 · Requests set and memory limit of 400Mi 🔴 · Namespaces, ConfigMaps and Secrets 🔴 · initContainers 🔴 · RBAC (ServiceAccount, Role, RoleBinding) 🔴 · NetworkPolicies 🔴 · ResourceQuotas 🔴

**Task 3: Observability and alerting**: Prometheus and Grafana via Helm 🔴 · Dashboard: node and per-pod CPU/memory 🔴 · Log aggregation (Loki, EFK or similar) 🔴 · Grafana alerts: health check and OOMKilled 🔴 · Alert routing to Slack or email (bonus) 🔴

**Documentation**: Setup, architecture and reproduction steps 🟡

## AI usage and originality evidence

| Signal | Where | What we saw | Points to |
|---|---|---|---|
| PR-driven history | `git log` | Four PRs, merge-backs, a 'Mrge branch' typo. | Against AI |

**AI read:** Hands-on.

**Originality:** No overlap.

## Verification questions for a screening call

1. What stopped you after Task 1?
2. How would you extend release.yml to update three Deployments?

## What would move them up

- Write the Task 2 manifests and ArgoCD Application.
- Add manifest tag updates to the release job.
