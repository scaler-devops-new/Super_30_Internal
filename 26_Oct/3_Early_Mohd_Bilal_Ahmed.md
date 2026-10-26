# Mohd Bilal Ahmed: Internal Technical Assessment (26 Oct 2026)

> **INTERNAL.** Second, deeper review. Includes AI-use, originality and placement guidance inferred from repository evidence and tool output. Confirm in conversation before acting on it.

**Repo:** [ahmedbilal-7191/devops-project](https://github.com/ahmedbilal-7191/devops-project)  
**Email:** ahmed.bilal7191@gmail.com

**Bottom line:** A creative 'three versions' idea (three Python base versions), but the release is broken by an env-var bug and the Kubernetes manifests are empty files.

## Placement read

| | |
|---|---|
| **Tier** | **Early**: Too little working output to judge, or stalled. |
| **Effect on placement chances** | Neutral |
| **Checkpoints (of 24)** | 4 done, 5 partial, 15 missing |
| **AI-generation likelihood** | Low-Medium |
| **Originality** | Clear |
| **Batch / module** | DevOps Oct24(1) / MLOps (Kashish Bhagat) |
| **Tech YOE (sheet)** | 0 |
| **Contest final** | Passed |
| **Mock interview** | No / n/a |
| **Calling feedback** | Submitted |

**Since 19 Oct:** One commit on 26 Oct (01:39): rewrote the release to a Python 3.11/3.12/3.13 matrix with a GitHub App token, and added four empty manifest files.

## Activity timeline

- **Own commits:** 4 across 3 active day(s): 10-15, 10-17, 10-26
- **First / last:** 2026-10-15 11:14 / 2026-10-26 01:39
- **PR merges:** 0 | **bot commits:** 0 | **branches:** main
- **Largest single commit:** +251 lines in 3 files (2026-10-15, "first commit")

<details><summary>Recent commits (newest last)</summary>

- `19bf367` 10-15 11:14 +251/-0 first commit
- `874e3f4` 10-15 15:42 +22/-0 Add Docker file
- `4d2bc2d` 10-17 13:25 +85/-0 add ci
- `a2055f9` 10-26 01:39 +105/-5 Task2 with manifest blue-print

</details>

## Technical deep dive

### Dockerfile
- `ARG PYTHON_VERSION`, slim-bookworm, UID 10001. No HEALTHCHECK, no EXPOSE. hadolint clean.

### PR pipeline
- Matrix of pytest, Flake8 and Bandit across three Pythons, plus Gitleaks, then build and Trivy gating.
- The image-build job references `matrix.python-version` but has no matrix (actionlint error), so it builds `PYTHON_VERSION=` with an empty tag.
- Replays still fail: Flake8 E302 in the starter test file, and Bandit scanning `test/` trips B310 (urlopen).

### Release pipeline
- The release builds `python-<ver>-sha-<short>` images to Docker Hub, then a GitHub App token commits manifest updates.
- **`MERGE_SHA: ${{ github.sha }} $(echo ... | cut -c1-7)` is set in an env block, where `$( )` is not evaluated.** The tag becomes the full SHA plus a literal shell string, which never matches the short-SHA tag that metadata-action pushed.

### Kubernetes
- `k8s-manifests/` holds four 0-byte files. Task 2 isn't started.

## Automated tool results

Run on the default branch. hadolint, actionlint, kubeconform (`-strict`), kube-linter, kustomize, promtool, shellcheck, gitleaks over full git history, and checkov. 'Replay' runs the tests from the repo root.

| Tool | Target | Result |
|---|---|---|
| hadolint | `Dockerfile` | clean |
| actionlint | `.github/workflows/cd.yml` | clean |
| actionlint | `.github/workflows/ci.yml` | property "python-version" is not defined in object type {} [expression]; property "python-version" is not defined in object type {} [expression] |
| gitleaks (full history) | `repo` | no leaks |
| checkov | `Dockerfile, K8s, GHA` | 1 failed checks (dockerfile 1) |
| pytest replay | `repo root` | 6 passed in 0.67s |

## Checkpoint scorecard

**Task 1: Docker and secure CI/CD**: Dockerfile (minimal base, non-root user) 🟢 · Branch protection (PR-only changes to main) 🔴 · PR pipeline: build the image 🟡 · PR pipeline: lint (Flake8) 🟡 · PR pipeline: SAST (B104 ignored) 🟡 · PR pipeline: secret scanning (optional) 🟢 · PR pipeline: unit tests (pytest) 🟢 · PR pipeline: image vulnerability scan 🟢 · Release pipeline: build and push to a registry 🟡 · Release pipeline: auto-update manifest image tags 🟡

**Task 2: GitOps with ArgoCD**: ArgoCD installed and syncing the repo 🔴 · Three Deployments behind one shared Service 🔴 · Requests set and memory limit of 400Mi 🔴 · Namespaces, ConfigMaps and Secrets 🔴 · initContainers 🔴 · RBAC (ServiceAccount, Role, RoleBinding) 🔴 · NetworkPolicies 🔴 · ResourceQuotas 🔴

**Task 3: Observability and alerting**: Prometheus and Grafana via Helm 🔴 · Dashboard: node and per-pod CPU/memory 🔴 · Log aggregation (Loki, EFK or similar) 🔴 · Grafana alerts: health check and OOMKilled 🔴 · Alert routing to Slack or email (bonus) 🔴

**Documentation**: Setup, architecture and reproduction steps 🔴

## AI usage and originality evidence

| Signal | Where | What we saw | Points to |
|---|---|---|---|
| Late-night single drop | `a2055f9` | 105-line rewrite at 01:39 using metadata-action v6 and create-github-app-token; never run. | Supports AI (weak) |
| Own-words comments | `ci.yml` | Typos and pasted brief text. | Against AI |

**AI read:** Mixed. The core is his own, and the new release may be assistant-drafted.

**Originality:** No overlap.

## Verification questions for a screening call

1. What value does MERGE_SHA actually hold at runtime?
2. Which tag does metadata-action push versus the tag your sed writes?

## What would move them up

- Fix lint and Bandit scope, the build matrix and MERGE_SHA.
- Write the actual manifests.
