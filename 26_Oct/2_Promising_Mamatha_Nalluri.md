# Mamatha Nalluri: Internal Technical Assessment (26 Oct 2026)

> **INTERNAL.** Second, deeper review. Includes AI-use, originality and placement guidance inferred from repository evidence and tool output. Confirm in conversation before acting on it.

**Repo:** [mamatha-nalluri/DevOps-SRE](https://github.com/mamatha-nalluri/DevOps-SRE)  
**Email:** nsmamatha60@gmail.com

**Bottom line:** A clean multi-stage Dockerfile with an isolated test stage and a solid PR and release pair, produced in one 40-minute burst. Needs a live check.

## Placement read

| | |
|---|---|
| **Tier** | **Promising**: Real skill visible; one more push or one live check. |
| **Effect on placement chances** | Neutral until verified |
| **Checkpoints (of 24)** | 6 done, 2 partial, 16 missing |
| **AI-generation likelihood** | Medium-High |
| **Originality** | Clear |
| **Batch / module** | DevOps Apr26 / DevOps Tools 1 (Tarun Asthana) |
| **Tech YOE (sheet)** | 5.67 |
| **Contest final** | Passed |
| **Mock interview** | No / n/a |
| **Calling feedback** | Will start today, will paste the link by eod |

**Since 19 Oct:** Went from starter-only to two merged PRs in a 40-minute burst on 22 Oct (21:37 to 22:17).

## Activity timeline

- **Own commits:** 11 across 2 active day(s): 10-16, 10-22
- **First / last:** 2026-10-16 18:43 / 2026-10-22 22:17
- **PR merges:** 2 | **bot commits:** 0 | **branches:** feat/container-ci-baseline, feat/dockerhub-release, main
- **Largest single commit:** +251 lines in 3 files (2026-10-16, "Initial commit")

<details><summary>Recent commits (newest last)</summary>

- `27afde8` 10-16 18:43 +251/-0 Initial commit
- `a7963fb` 10-22 21:37 +22/-0 Add hardened Python application container
- `65aca79` 10-22 21:38 +16/-0 Exclude local and sensitive files from Docker builds
- `40c798f` 10-22 21:38 +3/-0 Add development and CI quality tools
- `fe9618d` 10-22 21:38 +64/-0 Add pull request test and security pipeline
- `e050fd7` 10-22 21:40 +21/-3 Use multi-stage Docker build with isolated test stage
- `09ad131` 10-22 21:41 +5/-2 Test multi-stage Docker build in pull request CI
- `ec714c5` 10-22 21:52 +1/-1 Fix Trivy action reference to an available release
- `bc4f764` 10-22 22:08 +54/-0 Add Docker Hub release pipeline

</details>

## Technical deep dive

### Dockerfile
- Three stages: base, test (installs dev tools and runs pytest at build time), runtime (slim, UID 10001, HEALTHCHECK). Dev tooling never reaches runtime, the right pattern. hadolint clean.

### PR pipeline
- pytest, Bandit `-s B104 -ll`, Docker test-stage build, runtime build and Trivy gating. Concurrency group per PR.
- Flake8 only selects fatal error classes, so it isn't really linting. No secret scanning.

### Release pipeline
- On push to main: build runtime, Trivy-gate it, then push `sha-<full>` and latest to Docker Hub. No manifest update, because there is no Kubernetes yet.

## Automated tool results

Run on the default branch. hadolint, actionlint, kubeconform (`-strict`), kube-linter, kustomize, promtool, shellcheck, gitleaks over full git history, and checkov. 'Replay' runs the tests from the repo root.

| Tool | Target | Result |
|---|---|---|
| hadolint | `Dockerfile` | clean |
| actionlint | `.github/workflows/ci.yml` | clean |
| actionlint | `.github/workflows/release.yml` | clean |
| gitleaks (full history) | `repo` | no leaks |
| checkov | `Dockerfile, K8s, GHA` | 0 failed checks |
| pytest replay | `repo root` | 6 passed in 0.70s |

## Checkpoint scorecard

**Task 1: Docker and secure CI/CD**: Dockerfile (minimal base, non-root user) 🟢 · Branch protection (PR-only changes to main) 🟡 · PR pipeline: build the image 🟢 · PR pipeline: lint (Flake8) 🟡 · PR pipeline: SAST (B104 ignored) 🟢 · PR pipeline: secret scanning (optional) 🔴 · PR pipeline: unit tests (pytest) 🟢 · PR pipeline: image vulnerability scan 🟢 · Release pipeline: build and push to a registry 🟢 · Release pipeline: auto-update manifest image tags 🔴

**Task 2: GitOps with ArgoCD**: ArgoCD installed and syncing the repo 🔴 · Three Deployments behind one shared Service 🔴 · Requests set and memory limit of 400Mi 🔴 · Namespaces, ConfigMaps and Secrets 🔴 · initContainers 🔴 · RBAC (ServiceAccount, Role, RoleBinding) 🔴 · NetworkPolicies 🔴 · ResourceQuotas 🔴

**Task 3: Observability and alerting**: Prometheus and Grafana via Helm 🔴 · Dashboard: node and per-pod CPU/memory 🔴 · Log aggregation (Loki, EFK or similar) 🔴 · Grafana alerts: health check and OOMKilled 🔴 · Alert routing to Slack or email (bonus) 🔴

**Documentation**: Setup, architecture and reproduction steps 🔴

## AI usage and originality evidence

| Signal | Where | What we saw | Points to |
|---|---|---|---|
| Burst of complete files | `git log 22 Oct` | Eight commits in 40 minutes, each a finished file: Dockerfile at 21:37, .dockerignore and requirements at 21:38, a 64-line pipeline at 21:38. | Supports AI |
| Assistant-style commit messages | `git log` | 'Add hardened Python application container', 'Exclude local and sensitive files from Docker builds', 'Fix Trivy action reference to an available release'. | Supports AI |
| A real failure fixed | `ec714c5` | The Trivy reference was corrected 11 minutes after the pipeline landed, so something was run. | Against AI (weak) |

**AI read:** Likely produced with an AI assistant in one sitting. It's still good work if she understands it, but that needs confirming.

**Originality:** No overlap.

## Verification questions for a screening call

1. Why have a separate test stage, and what happens if tests fail during docker build?
2. Your Flake8 only checks E9, F63, F7 and F82. What does that miss?
3. Walk me through what you changed after the first Trivy failure.

## What would move them up

- Real Flake8 and a secret scan.
- Task 2 manifests, ArgoCD and the tag-update step.
