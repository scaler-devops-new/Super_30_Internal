# Shreya Khare: Internal Technical Assessment (26 Oct 2026)

> **INTERNAL.** Second, deeper review. Includes AI-use, originality and placement guidance inferred from repository evidence and tool output. Confirm in conversation before acting on it.

**Repo:** [shreya1302/DevOps-SRE](https://github.com/shreya1302/DevOps-SRE)  
**Email:** khareshreya1302@gmail.com

**Bottom line:** A tidy PR-driven Task 1 that tried to fix CVEs properly before relaxing the gate.

## Placement read

| | |
|---|---|
| **Tier** | **Promising**: Real skill visible; one more push or one live check. |
| **Effect on placement chances** | Increase |
| **Checkpoints (of 24)** | 6 done, 2 partial, 16 missing |
| **AI-generation likelihood** | Low |
| **Originality** | Clear |
| **Batch / module** | DevOps Apr26 / DevOps Tools 1 (Tarun Asthana) |
| **Tech YOE (sheet)** | 4.25 |
| **Contest final** | Passed |
| **Mock interview** | No / n/a |
| **Calling feedback** | Have started. Will submit within an hour |

**Since 19 Oct:** On 21 Oct added image build and Trivy to the PR pipeline and a GHCR release workflow, merged via PRs #2 and #3.

## Activity timeline

- **Own commits:** 10 across 2 active day(s): 10-16, 10-21
- **First / last:** 2026-10-16 17:08 / 2026-10-21 18:01
- **PR merges:** 3 | **bot commits:** 0 | **branches:** feature/ci-docker-trivy, feature/ghcr-release, feature/github-actions-ci, main
- **Largest single commit:** +94 lines in 7 files (2026-10-16, "Add Python CI workflow and code quality checks")

<details><summary>Recent commits (newest last)</summary>

- `7eee7a7` 10-16 17:08 +94/-13 Add Python CI workflow and code quality checks
- `9932afd` 10-21 17:15 +11/-0 Add Docker build and trivy scan to CI
- `be3954e` 10-21 17:21 +1/-1 Fix Trivy action version reference
- `25f1400` 10-21 17:28 +1/-1 Update python slim base image
- `e25319d` 10-21 17:35 +2/-1 Upgrade Debian packages in Docker image
- `8c9484e` 10-21 17:43 +1/-1 Fail Trivy CI only on critical vulnerabilities
- `5c6dfb9` 10-21 17:58 +46/-0 Add GHCR release workflow

</details>

## Technical deep dive

### Dockerfile
- python:3.12-slim-trixie with `apt-get upgrade -y` to clear OS CVEs, system user, no HEALTHCHECK.
- `apt-get upgrade` in a Dockerfile makes builds non-reproducible. Bumping or pinning the base image is the cleaner fix.

### PR pipeline
- pytest, Flake8 (app.py reformatted, so it passes), Bandit with inline `# nosec B104`, build and Trivy. Replay passes.
- She tried a newer base and an apt upgrade first, then narrowed Trivy to CRITICAL only. The order shows she understood the trade-off.

### Release pipeline
- On push to main, metadata-action `sha` tags to GHCR. No manifest update, because there are no manifests.

## Automated tool results

Run on the default branch. hadolint, actionlint, kubeconform (`-strict`), kube-linter, kustomize, promtool, shellcheck, gitleaks over full git history, and checkov. 'Replay' runs the tests from the repo root.

| Tool | Target | Result |
|---|---|---|
| hadolint | `Dockerfile` | clean |
| actionlint | `.github/workflows/ci.yml` | clean |
| actionlint | `.github/workflows/release.yml` | clean |
| gitleaks (full history) | `repo` | no leaks |
| checkov | `Dockerfile, K8s, GHA` | 1 failed checks (dockerfile 1) |
| pytest replay | `repo root` | 6 passed in 0.72s |

## Checkpoint scorecard

**Task 1: Docker and secure CI/CD**: Dockerfile (minimal base, non-root user) 🟢 · Branch protection (PR-only changes to main) 🟡 · PR pipeline: build the image 🟢 · PR pipeline: lint (Flake8) 🟢 · PR pipeline: SAST (B104 ignored) 🟢 · PR pipeline: secret scanning (optional) 🔴 · PR pipeline: unit tests (pytest) 🟢 · PR pipeline: image vulnerability scan 🟡 · Release pipeline: build and push to a registry 🟢 · Release pipeline: auto-update manifest image tags 🔴

**Task 2: GitOps with ArgoCD**: ArgoCD installed and syncing the repo 🔴 · Three Deployments behind one shared Service 🔴 · Requests set and memory limit of 400Mi 🔴 · Namespaces, ConfigMaps and Secrets 🔴 · initContainers 🔴 · RBAC (ServiceAccount, Role, RoleBinding) 🔴 · NetworkPolicies 🔴 · ResourceQuotas 🔴

**Task 3: Observability and alerting**: Prometheus and Grafana via Helm 🔴 · Dashboard: node and per-pod CPU/memory 🔴 · Log aggregation (Loki, EFK or similar) 🔴 · Grafana alerts: health check and OOMKilled 🔴 · Alert routing to Slack or email (bonus) 🔴

**Documentation**: Setup, architecture and reproduction steps 🔴

## AI usage and originality evidence

| Signal | Where | What we saw | Points to |
|---|---|---|---|
| Iterative CVE work | `git log 21 Oct` | Base bump, apt upgrade, then threshold change, one PR. | Against AI |

**AI read:** Hands-on.

**Originality:** No overlap.

## Verification questions for a screening call

1. Why apt-get upgrade instead of a newer base tag?
2. Which HIGH CVEs remain, and are they exploitable here?

## What would move them up

- Add Gitleaks and a HEALTHCHECK.
- Task 2 manifests and ArgoCD.
