# Alfiya Javed: Internal Technical Assessment (26 Oct 2026)

> **INTERNAL.** Second, deeper review. Includes AI-use, originality and placement guidance inferred from repository evidence and tool output. Confirm in conversation before acting on it.

**Repo:** [02alfiya/DevOps-SRE](https://github.com/02alfiya/DevOps-SRE)  
**Email:** alfiyatamboli8@gmail.com

**Bottom line:** Only a Dockerfile, with two mistakes.

## Placement read

| | |
|---|---|
| **Tier** | **Early**: Too little working output to judge, or stalled. |
| **Effect on placement chances** | Neutral |
| **Checkpoints (of 24)** | 0 done, 1 partial, 23 missing |
| **AI-generation likelihood** | Low |
| **Originality** | Clear |
| **Batch / module** | DevOps Apr25 / AWS 2 (Raham Shaik) |
| **Tech YOE (sheet)** | 0 |
| **Contest final** | Passed |
| **Mock interview** | No / n/a |
| **Calling feedback** | Said will submit it within 2 hrs |

**Since 19 Oct:** New to the review. Dockerfile added 21 Oct.

## Activity timeline

- **Own commits:** 2 across 2 active day(s): 10-16, 10-21
- **First / last:** 2026-10-16 13:53 / 2026-10-21 23:45
- **PR merges:** 0 | **bot commits:** 0 | **branches:** main
- **Largest single commit:** +251 lines in 3 files (2026-10-16, "Initial commit")

<details><summary>Recent commits (newest last)</summary>

- `c02b20d` 10-16 13:53 +251/-0 Initial commit
- `8c353fa` 10-21 23:45 +19/-0 Add Dockerfile: alpine base, non-root user, port 8080

</details>

## Technical deep dive

### Dockerfile
- python:3.14-alpine, UID 10001. **`EXPOSE 8000` while the app listens on 8080**, and the shell path `/sbin/nplogin` is a typo for nologin. No HEALTHCHECK.

### Everything else
- Not started.

## Automated tool results

Run on the default branch. hadolint, actionlint, kubeconform (`-strict`), kube-linter, kustomize, promtool, shellcheck, gitleaks over full git history, and checkov. 'Replay' runs the tests from the repo root.

| Tool | Target | Result |
|---|---|---|
| hadolint | `Dockerfile` | clean |
| gitleaks (full history) | `repo` | no leaks |
| checkov | `Dockerfile, K8s, GHA` | 1 failed checks (dockerfile 1) |
| pytest replay | `repo root` | 6 passed in 0.74s |

## Checkpoint scorecard

**Task 1: Docker and secure CI/CD**: Dockerfile (minimal base, non-root user) 🟡 · Branch protection (PR-only changes to main) 🔴 · PR pipeline: build the image 🔴 · PR pipeline: lint (Flake8) 🔴 · PR pipeline: SAST (B104 ignored) 🔴 · PR pipeline: secret scanning (optional) 🔴 · PR pipeline: unit tests (pytest) 🔴 · PR pipeline: image vulnerability scan 🔴 · Release pipeline: build and push to a registry 🔴 · Release pipeline: auto-update manifest image tags 🔴

**Task 2: GitOps with ArgoCD**: ArgoCD installed and syncing the repo 🔴 · Three Deployments behind one shared Service 🔴 · Requests set and memory limit of 400Mi 🔴 · Namespaces, ConfigMaps and Secrets 🔴 · initContainers 🔴 · RBAC (ServiceAccount, Role, RoleBinding) 🔴 · NetworkPolicies 🔴 · ResourceQuotas 🔴

**Task 3: Observability and alerting**: Prometheus and Grafana via Helm 🔴 · Dashboard: node and per-pod CPU/memory 🔴 · Log aggregation (Loki, EFK or similar) 🔴 · Grafana alerts: health check and OOMKilled 🔴 · Alert routing to Slack or email (bonus) 🔴

**Documentation**: Setup, architecture and reproduction steps 🔴

## AI usage and originality evidence

| Signal | Where | What we saw | Points to |
|---|---|---|---|
| Typos | `Dockerfile` | nplogin, wrong port. | Against AI |

**AI read:** Hands-on, minimal.

**Originality:** No overlap.

## Verification questions for a screening call

1. Which port does the app listen on?

## What would move them up

- Fix the port and start the PR pipeline.
