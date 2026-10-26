# Nikhil Mourya: Internal Technical Assessment (26 Oct 2026)

> **INTERNAL.** Second, deeper review. Includes AI-use, originality and placement guidance inferred from repository evidence and tool output. Confirm in conversation before acting on it.

**Repo:** [nikhilmourya36/Devops-memory-leak-microservice-challenge](https://github.com/nikhilmourya36/Devops-memory-leak-microservice-challenge)  
**Email:** nikhilmourya36@gmail.com

**Bottom line:** Unchanged: copied pipeline code with introduced bugs, never successfully run.

## Placement read

| | |
|---|---|
| **Tier** | **Concern**: Originality or ownership concern; lower priority. |
| **Effect on placement chances** | Decrease |
| **Checkpoints (of 24)** | 1 done, 8 partial, 15 missing |
| **AI-generation likelihood** | Medium |
| **Originality** | Flagged (matches Amit) |
| **Batch / module** | DevOps Aug25 / AWS 2 (Raham Shaik) |
| **Tech YOE (sheet)** | 2.75 |
| **Contest final** | Passed |
| **Mock interview** | No / n/a |
| **Calling feedback** | Will submit by today EOD |

**Since 19 Oct:** No commits since 17 Oct. The sheet says 'Will submit by today EOD'.

## Activity timeline

- **Own commits:** 2 across 1 active day(s): 10-17
- **First / last:** 2026-10-17 15:27 / 2026-10-17 20:44
- **PR merges:** 0 | **bot commits:** 0 | **branches:** main
- **Largest single commit:** +165 lines in 4 files (2026-10-17, "adding k8s and worlflow pipline")

<details><summary>Recent commits (newest last)</summary>

- `44e9cb4` 10-17 15:27 +1/-0 adding gitignore
- `2c17af1` 10-17 20:44 +165/-0 adding k8s and worlflow pipline

</details>

## Technical deep dive

### Pipelines
- Dockerfile in `app/` but builds from root; invalid image ref `:v1:<sha>`; Bandit without B104 skip; Flake8 fails.

### Kubernetes
- One Deployment with no resources (kube-linter: unset-memory-requirements).

## Automated tool results

Run on the default branch. hadolint, actionlint, kubeconform (`-strict`), kube-linter, kustomize, promtool, shellcheck, gitleaks over full git history, and checkov. 'Replay' runs the tests from the repo root.

| Tool | Target | Result |
|---|---|---|
| hadolint | `app/Dockerfile` | clean |
| actionlint | `.github/workflows/pr-validation.yml` | clean |
| actionlint | `.github/workflows/release.yml` | clean |
| kubeconform | `all manifests` | 1 resource found in 1 file - Valid: 1, Invalid: 0, Errors: 0, Skipped: 0 |
| kube-linter | `all manifests` | no-read-only-root-fs x1, run-as-non-root x1, unset-cpu-requirements x1, unset-memory-requirements x1 |
| gitleaks (full history) | `repo` | no leaks |
| checkov | `Dockerfile, K8s, GHA` | 21 failed checks (dockerfile 1, kubernetes 20) |
| pytest replay | `repo root` | 6 passed in 0.62s |

## Checkpoint scorecard

**Task 1: Docker and secure CI/CD**: Dockerfile (minimal base, non-root user) 🟡 · Branch protection (PR-only changes to main) 🔴 · PR pipeline: build the image 🟡 · PR pipeline: lint (Flake8) 🟡 · PR pipeline: SAST (B104 ignored) 🟡 · PR pipeline: secret scanning (optional) 🔴 · PR pipeline: unit tests (pytest) 🟢 · PR pipeline: image vulnerability scan 🟡 · Release pipeline: build and push to a registry 🟡 · Release pipeline: auto-update manifest image tags 🟡

**Task 2: GitOps with ArgoCD**: ArgoCD installed and syncing the repo 🔴 · Three Deployments behind one shared Service 🟡 · Requests set and memory limit of 400Mi 🔴 · Namespaces, ConfigMaps and Secrets 🔴 · initContainers 🔴 · RBAC (ServiceAccount, Role, RoleBinding) 🔴 · NetworkPolicies 🔴 · ResourceQuotas 🔴

**Task 3: Observability and alerting**: Prometheus and Grafana via Helm 🔴 · Dashboard: node and per-pod CPU/memory 🔴 · Log aggregation (Loki, EFK or similar) 🔴 · Grafana alerts: health check and OOMKilled 🔴 · Alert routing to Slack or email (bonus) 🔴

**Documentation**: Setup, architecture and reproduction steps 🔴

## AI usage and originality evidence

| Signal | Where | What we saw | Points to |
|---|---|---|---|
| Near-verbatim match | `release.yml` | Tag-update block identical to Amit's apart from one string. | Copying |

**AI read:** Copied or co-generated, untested.

**Originality:** Flagged against Amit (Amit's version is earlier).

## Verification questions for a screening call

1. Where did the release workflow's Python block come from?

## What would move them up

- Original, working Task 1.
