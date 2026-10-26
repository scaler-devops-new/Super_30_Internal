# Harshal Machhi: Internal Technical Assessment (26 Oct 2026)

> **INTERNAL.** Second, deeper review. Includes AI-use, originality and placement guidance inferred from repository evidence and tool output. Confirm in conversation before acting on it.

**Repo:** [Harshuqt/Scaler_DevSecOps_and_SRE_Lab](https://github.com/Harshuqt/Scaler_DevSecOps_and_SRE_Lab)  
**Email:** 32harshalmachhi@gmail.com

**Bottom line:** Unchanged: a single Dockerfile identical to Shishir's, including the wrong EXPOSE 80.

## Placement read

| | |
|---|---|
| **Tier** | **Concern**: Originality or ownership concern; lower priority. |
| **Effect on placement chances** | Decrease |
| **Checkpoints (of 24)** | 0 done, 1 partial, 23 missing |
| **AI-generation likelihood** | Low |
| **Originality** | Flagged (matches Shishir) |
| **Batch / module** | DevOps Oct25 / DevOps Tools 2 (Akshat Gupta) |
| **Tech YOE (sheet)** | 0 |
| **Contest final** | Passed |
| **Mock interview** | completed / #N/A |
| **Calling feedback** | Have started. Will submit within an hour |

**Since 19 Oct:** No commits since 16 Oct. The sheet says 'Will submit within an hour'.

## Activity timeline

- **Own commits:** 2 across 1 active day(s): 10-16
- **First / last:** 2026-10-16 14:03 / 2026-10-16 14:09
- **PR merges:** 0 | **bot commits:** 0 | **branches:** main
- **Largest single commit:** +258 lines in 4 files (2026-10-16, "Added Project And Dockerfiles")

<details><summary>Recent commits (newest last)</summary>

- `f3f4b44` 10-16 14:03 +258/-0 Added Project And Dockerfiles
- `aba1d1d` 10-16 14:09 +1/-1 Switch Python Docker base image from slim to alpine

</details>

## Technical deep dive

### Dockerfile
- Byte-identical to Shishir's 16 Oct version (similarity 1.0 on normalised lines).

## Automated tool results

Run on the default branch. hadolint, actionlint, kubeconform (`-strict`), kube-linter, kustomize, promtool, shellcheck, gitleaks over full git history, and checkov. 'Replay' runs the tests from the repo root.

| Tool | Target | Result |
|---|---|---|
| hadolint | `Dockerfile` | clean |
| gitleaks (full history) | `repo` | no leaks |
| checkov | `Dockerfile, K8s, GHA` | 1 failed checks (dockerfile 1) |
| pytest replay | `repo root` | 6 passed in 0.68s |

## Checkpoint scorecard

**Task 1: Docker and secure CI/CD**: Dockerfile (minimal base, non-root user) 🟡 · Branch protection (PR-only changes to main) 🔴 · PR pipeline: build the image 🔴 · PR pipeline: lint (Flake8) 🔴 · PR pipeline: SAST (B104 ignored) 🔴 · PR pipeline: secret scanning (optional) 🔴 · PR pipeline: unit tests (pytest) 🔴 · PR pipeline: image vulnerability scan 🔴 · Release pipeline: build and push to a registry 🔴 · Release pipeline: auto-update manifest image tags 🔴

**Task 2: GitOps with ArgoCD**: ArgoCD installed and syncing the repo 🔴 · Three Deployments behind one shared Service 🔴 · Requests set and memory limit of 400Mi 🔴 · Namespaces, ConfigMaps and Secrets 🔴 · initContainers 🔴 · RBAC (ServiceAccount, Role, RoleBinding) 🔴 · NetworkPolicies 🔴 · ResourceQuotas 🔴

**Task 3: Observability and alerting**: Prometheus and Grafana via Helm 🔴 · Dashboard: node and per-pod CPU/memory 🔴 · Log aggregation (Loki, EFK or similar) 🔴 · Grafana alerts: health check and OOMKilled 🔴 · Alert routing to Slack or email (bonus) 🔴

**Documentation**: Setup, architecture and reproduction steps 🔴

## AI usage and originality evidence

| Signal | Where | What we saw | Points to |
|---|---|---|---|
| Exact copy | `Dockerfile` | Same lines, same mistake, plus a broken slim/Alpine hybrid first version fixed six minutes later. | Copying |

**AI read:** Not an AI case.

**Originality:** High-confidence copy of Shishir's file (same batch).

## Verification questions for a screening call

1. Walk me through your Dockerfile line by line.

## What would move them up

- Original work.
