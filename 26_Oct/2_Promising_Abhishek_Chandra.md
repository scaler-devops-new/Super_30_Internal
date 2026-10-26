# Abhishek Chandra: Internal Technical Assessment (26 Oct 2026)

> **INTERNAL.** Second, deeper review. Includes AI-use, originality and placement guidance inferred from repository evidence and tool output. Confirm in conversation before acting on it.

**Repo:** [abhishekchandra2522k/devsecops-sre-project](https://github.com/abhishekchandra2522k/devsecops-sre-project)  
**Email:** 1ac23456789@gmail.com

**Bottom line:** Branch protection as code plus a tidy PR gate. But he relaxed two controls to get green, and the release work is unmerged.

## Placement read

| | |
|---|---|
| **Tier** | **Promising**: Real skill visible; one more push or one live check. |
| **Effect on placement chances** | Slight increase |
| **Checkpoints (of 24)** | 7 done, 5 partial, 12 missing |
| **AI-generation likelihood** | Low |
| **Originality** | Clear |
| **Batch / module** | DevOps Feb26 / DevOps Tools 1 (Vilas Varghese) |
| **Tech YOE (sheet)** | 3.5 |
| **Contest final** | Passed |
| **Mock interview** | No / n/a |
| **Calling feedback** | Submitted |

**Since 19 Oct:** PR pipeline added 21 to 22 Oct and merged via PR #1. Release work on `feature/release_enhancement` (25 Oct) is not merged.

## Activity timeline

- **Own commits:** 17 across 4 active day(s): 10-15, 10-21, 10-22, 10-25
- **First / last:** 2026-10-15 12:19 / 2026-10-25 23:31
- **PR merges:** 1 | **bot commits:** 0 | **branches:** feature/pr-validation, feature/release_enhancement, main
- **Largest single commit:** +282 lines in 8 files (2026-10-15, "Add Dockerfile, python-app, kind_installation script, ruleset configur")

<details><summary>Recent commits (newest last)</summary>

- `85e5488` 10-15 15:12 +282/-0 Add Dockerfile, python-app, kind_installation script, ruleset configuration
- `1fe367f` 10-15 15:18 +1/-0 Add admin write permisson for the short lived github token
- `17e2dc2` 10-15 15:31 +38/-12 Add new token with admin read write permissions and few more checks in the script
- `23377ed` 10-15 15:51 +0/-2 remove workflow dispatch
- `4e95dfc` 10-15 15:53 +10/-34 testing workflow
- `c19b7d5` 10-15 15:56 +2/-0 add workflow dispatch
- `f64edab` 10-21 20:48 +89/-0 add pr validation workflow
- `ca906df` 10-21 20:53 +2/-2 fix pr validation yaml
- `f65d165` 10-21 21:04 +2/-1 fix for validation yaml
- `a90289c` 10-21 21:32 +25/-13 update
- `28e5bec` 10-21 21:55 +5/-1 add token for secret scanning
- `30f16f4` 10-22 20:27 +12/-5 fix vulnerabilities
- `ace98f5` 10-22 20:32 +1/-1 update required approvers to 0
- `62067d3` 10-25 21:31 +126/-0 initial enhancement
- `4b9f8c1` 10-25 23:31 +60/-47 update release

</details>

## Technical deep dive

### Dockerfile
- python:3.13-slim, UID 10001, compileall at build time, HEALTHCHECK. hadolint clean.

### PR pipeline
- Flake8, pytest, Bandit `-s B104 -ll`, Gitleaks v3 with PR comments, build, smoke test (compileall inside the image) and Trivy. Concurrency cancel-in-progress. Replay passes.
- **Trivy was narrowed from HIGH,CRITICAL to CRITICAL in a commit titled 'fix vulnerabilities'.** The vulnerabilities weren't fixed; the gate was lowered.
- The ruleset's `required_approving_review_count` was dropped from 1 to 0 so he can self-merge. That's understandable solo, but it should be stated.

### Release pipeline (branch only)
- Push to main, build to GHCR as `sha-<7>` and latest with a GHA cache, then a second job sed-updates `k8s/deployment.yaml` using a bypass token, with a comment explaining why GITHUB_TOKEN can't push to a protected branch. Good reasoning.
- Only one Deployment (`:latest` tag, no probes, no securityContext), and it isn't merged.

## Automated tool results

Run on the default branch. hadolint, actionlint, kubeconform (`-strict`), kube-linter, kustomize, promtool, shellcheck, gitleaks over full git history, and checkov. 'Replay' runs the tests from the repo root.

| Tool | Target | Result |
|---|---|---|
| hadolint | `Dockerfile` | clean |
| actionlint | `.github/workflows/pr-validation.yaml` | clean |
| actionlint | `.github/workflows/rulesets.yaml` | 1 shellcheck style notes only |
| shellcheck | `kind_installation.sh` | clean |
| gitleaks (full history) | `repo` | no leaks |
| checkov | `Dockerfile, K8s, GHA` | 0 failed checks |
| pytest replay | `repo root` | 6 passed in 0.59s |

## Checkpoint scorecard

**Task 1: Docker and secure CI/CD**: Dockerfile (minimal base, non-root user) 🟢 · Branch protection (PR-only changes to main) 🟢 · PR pipeline: build the image 🟢 · PR pipeline: lint (Flake8) 🟢 · PR pipeline: SAST (B104 ignored) 🟢 · PR pipeline: secret scanning (optional) 🟢 · PR pipeline: unit tests (pytest) 🟢 · PR pipeline: image vulnerability scan 🟡 · Release pipeline: build and push to a registry 🟡 · Release pipeline: auto-update manifest image tags 🟡

**Task 2: GitOps with ArgoCD**: ArgoCD installed and syncing the repo 🔴 · Three Deployments behind one shared Service 🟡 · Requests set and memory limit of 400Mi 🟡 · Namespaces, ConfigMaps and Secrets 🔴 · initContainers 🔴 · RBAC (ServiceAccount, Role, RoleBinding) 🔴 · NetworkPolicies 🔴 · ResourceQuotas 🔴

**Task 3: Observability and alerting**: Prometheus and Grafana via Helm 🔴 · Dashboard: node and per-pod CPU/memory 🔴 · Log aggregation (Loki, EFK or similar) 🔴 · Grafana alerts: health check and OOMKilled 🔴 · Alert routing to Slack or email (bonus) 🔴

**Documentation**: Setup, architecture and reproduction steps 🔴

## AI usage and originality evidence

| Signal | Where | What we saw | Points to |
|---|---|---|---|
| Iterating on token scopes | `git log` | Multiple permission fixes on the ruleset workflow. | Against AI |
| Explanatory comments | `release.yaml (branch)` | Clear reasoning comments; plausible own or assisted. | Neutral |

**AI read:** Hands-on.

**Originality:** No overlap.

## Verification questions for a screening call

1. What HIGH CVEs were in the image when you narrowed Trivy to CRITICAL?
2. Who can bypass your ruleset, and why does the release need that?

## What would move them up

- Fix the base image CVEs instead of lowering the gate.
- Merge the release branch, then three Deployments and ArgoCD.
