# SAMBANA MANOJ KUMAR: Internal Technical Assessment (26 Oct 2026)

> **INTERNAL.** Second, deeper review. Includes AI-use, originality and placement guidance inferred from repository evidence and tool output. Confirm in conversation before acting on it.

**Repo:** [manojsambana443/devops-sre-project](https://github.com/manojsambana443/devops-sre-project)  
**Email:** manojsambana443@gmail.com

**Bottom line:** The only learner who built truly different image versions. But there is still no CI at all, and ArgoCD only tracks one of the three Deployments.

## Placement read

| | |
|---|---|
| **Tier** | **Promising**: Real skill visible; one more push or one live check. |
| **Effect on placement chances** | Slight increase |
| **Checkpoints (of 24)** | 4 done, 6 partial, 14 missing |
| **AI-generation likelihood** | Medium |
| **Originality** | Clear |
| **Batch / module** | DevOps Dec24(1) / High Level Design (Pragy Agarwal) |
| **Tech YOE (sheet)** | 2.08 |
| **Contest final** | Passed |
| **Mock interview** | completed / Hire |
| **Calling feedback** | Not yet started. Will start this weekend. |

**Since 19 Oct:** Two commits (25 to 26 Oct) added 670 lines of Kubernetes and GitOps manifests, plus genuinely different v2 and v3 app variants.

## Activity timeline

- **Own commits:** 5 across 3 active day(s): 10-16, 10-25, 10-26
- **First / last:** 2026-10-16 07:57 / 2026-10-26 08:37
- **PR merges:** 0 | **bot commits:** 0 | **branches:** main
- **Largest single commit:** +593 lines in 20 files (2026-10-25, "Add Kubernetes manifests and GitOps foundation")

<details><summary>Recent commits (newest last)</summary>

- `e2fc0a5` 10-16 07:57 +35/-0 Add Dockerfile and development configuration
- `b80d291` 10-16 08:09 +48/-0 Document project progress and assessment requirements
- `e6007bd` 10-16 08:17 +0/-78 Keep README focused on project progress
- `5ddabcd` 10-25 10:43 +593/-1 Add Kubernetes manifests and GitOps foundation
- `2ebf1c7` 10-26 08:37 +78/-1 Complete GitOps application manifests

</details>

## Technical deep dive

### Dockerfile
- python:3.12-slim, system user, `ARG APP_SOURCE` so one Dockerfile builds v1, v2 and v3 from `versions/vN/app`. Smart reuse. No HEALTHCHECK.

### CI/CD
- **No workflows at all, 10 days in.** Images are local tags (`memory-app:v1`) loaded into the cluster by hand.

### Kubernetes
- `k8s/`: three Deployments, one Service, ConfigMap, LimitRange, ResourceQuota, ServiceAccount, Role/RoleBinding, NetworkPolicy, and a busybox initContainer that validates required env vars. kubeconform strict: 21/21 valid.
- No Deployment sets `serviceAccountName`, so the ServiceAccount and Role are never used. No Secret.
- The NetworkPolicy only admits pods labelled `access: allowed`, and nothing in the repo has that label, so in practice it is deny-all.

### GitOps
- An ArgoCD Application with automated selfHeal tracks `gitops/base`, which contains only the v1 Deployment. Two parallel manifest trees (`k8s/` and `gitops/base`) will drift.

## Automated tool results

Run on the default branch. hadolint, actionlint, kubeconform (`-strict`), kube-linter, kustomize, promtool, shellcheck, gitleaks over full git history, and checkov. 'Replay' runs the tests from the repo root.

| Tool | Target | Result |
|---|---|---|
| hadolint | `Dockerfile` | clean |
| kubeconform | `all manifests` | 22 resources found in 18 files - Valid: 21, Invalid: 0, Errors: 0, Skipped: 1 |
| kube-linter | `all manifests` | unset-cpu-requirements x5, no-read-only-root-fs x4, run-as-non-root x4, unset-memory-requirements x3 |
| gitleaks (full history) | `repo` | no leaks |
| checkov | `Dockerfile, K8s, GHA` | 65 failed checks (dockerfile 1, kubernetes 64) |
| pytest replay | `repo root` | 6 passed in 0.77s |

## Checkpoint scorecard

**Task 1: Docker and secure CI/CD**: Dockerfile (minimal base, non-root user) 🟢 · Branch protection (PR-only changes to main) 🔴 · PR pipeline: build the image 🔴 · PR pipeline: lint (Flake8) 🔴 · PR pipeline: SAST (B104 ignored) 🔴 · PR pipeline: secret scanning (optional) 🔴 · PR pipeline: unit tests (pytest) 🔴 · PR pipeline: image vulnerability scan 🔴 · Release pipeline: build and push to a registry 🔴 · Release pipeline: auto-update manifest image tags 🔴

**Task 2: GitOps with ArgoCD**: ArgoCD installed and syncing the repo 🟡 · Three Deployments behind one shared Service 🟡 · Requests set and memory limit of 400Mi 🟢 · Namespaces, ConfigMaps and Secrets 🟡 · initContainers 🟢 · RBAC (ServiceAccount, Role, RoleBinding) 🟡 · NetworkPolicies 🟡 · ResourceQuotas 🟢

**Task 3: Observability and alerting**: Prometheus and Grafana via Helm 🔴 · Dashboard: node and per-pod CPU/memory 🔴 · Log aggregation (Loki, EFK or similar) 🔴 · Grafana alerts: health check and OOMKilled 🔴 · Alert routing to Slack or email (bonus) 🔴

**Documentation**: Setup, architecture and reproduction steps 🟡

## AI usage and originality evidence

| Signal | Where | What we saw | Points to |
|---|---|---|---|
| Large polished drop | `5ddabcd` | 593 lines across 20 files in one commit, with refactored app variants (Content-Length header, logger.exception). | Supports AI |
| Honest progress README | `readme.md` | Accurately lists done and pending items. | Against AI |

**AI read:** Probably AI-assisted for the manifests, but the design choices (real variants, a validation initContainer) are coherent and his earlier work was verified hands-on.

**Originality:** No overlap.

## Verification questions for a screening call

1. Why does ArgoCD track gitops/base when the three Deployments live in k8s/?
2. Which pods can reach your app through the NetworkPolicy?
3. How did you get the images into the cluster?

## What would move them up

- Build the PR and release pipelines (overdue).
- Merge to one manifest tree and point ArgoCD at it.
- Wire serviceAccountName and add a Secret.
