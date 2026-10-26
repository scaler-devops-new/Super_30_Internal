# Amit Chhabra: Internal Technical Assessment (26 Oct 2026)

> **INTERNAL.** Second, deeper review. Includes AI-use, originality and placement guidance inferred from repository evidence and tool output. Confirm in conversation before acting on it.

**Repo:** [amit199222/DevOps-SRE](https://github.com/amit199222/DevOps-SRE)  
**Email:** amit199222@gmail.com

**Bottom line:** Strong Kubernetes footprint and real debugging history, but last week's untested change to main broke the release pipeline.

## Placement read

| | |
|---|---|
| **Tier** | **Strong**: Clear positive signal; put forward now (gaps noted). |
| **Effect on placement chances** | Increase, but note the regression |
| **Checkpoints (of 24)** | 14 done, 5 partial, 5 missing |
| **AI-generation likelihood** | Low |
| **Originality** | Shared code (source, with Nikhil) |
| **Batch / module** | DevOps Feb25 / High Level Design (Pragy Agarwal) |
| **Tech YOE (sheet)** | 12 |
| **Contest final** | Passed |
| **Mock interview** | No / n/a |
| **Calling feedback** | Have started working on it. Will submit by tomorrow |

**Since 19 Oct:** One commit on 24 Oct ('project improvements', straight to main): added Gitleaks to the PR pipeline, pointed ArgoCD at main, and replaced the regex tag updater with yq. The yq approach is right, but the change is untested and breaks the release job.

## Activity timeline

- **Own commits:** 12 across 2 active day(s): 10-17, 10-24
- **First / last:** 2026-10-17 10:08 / 2026-10-24 13:27
- **PR merges:** 1 | **bot commits:** 0 | **branches:** automation/update-image-eb6acf4e7343bfade4e373981c70be6ff5a79f9f, feature/my-work, main
- **Largest single commit:** +386 lines in 10 files (2026-10-17, "task 2")

<details><summary>Recent commits (newest last)</summary>

- `b1d85b4` 10-17 10:08 +1/-1 chore(gitops): update memory service image
- `53dedaa` 10-17 12:15 +257/-0 task 1
- `72c38a7` 10-17 12:43 +2/-2 fix trivy
- `a2c4e46` 10-17 12:48 +2/-2 fix trivy
- `cb6f206` 10-17 12:50 +0/-1 fix trivy
- `5b72901` 10-17 13:55 +386/-10 task 2
- `c8ef189` 10-17 14:28 +31/-0 k8s non root issue fix
- `b46cc68` 10-17 14:43 +1/-1 increase memory quota
- `934d007` 10-17 15:07 +5/-4 update docker file
- `d12c3d9` 10-17 15:39 +58/-0 helm
- `62bf9b6` 10-24 13:27 +42/-36 project improvements

</details>

## Technical deep dive

### Dockerfile
- python:3.12-slim, UID 65532, HEALTHCHECK added this week. hadolint clean.

### PR pipeline
- pytest, Flake8 (with a `.flake8` that ignores the starter's whitespace codes), Bandit `-s B104 -ll`, Gitleaks (new), build and Trivy gating on HIGH/CRITICAL. Replay: all pass.

### Release pipeline
- Triggers on a merged PR, re-scans before pushing, and pushes `ghcr.io/<repo>:<sha>`, the strictest reading of the brief.
- **Broken since 24 Oct:** the tag step reads `${{ steps.meta.outputs.version }}`, but there is no step with id `meta` (actionlint: property "meta" is not defined). IMAGE_TAG is empty, so manifests would get `ghcr.io/amit199222/DevOps-SRE:-v1`. That is invalid twice: an uppercase repo path and an empty tag.
- Even with that fixed, the pipeline pushes `:<sha>` while manifests reference `:<sha>-v1/-v2/-v3`, tags that are never built.
- The committed manifests currently point at hand-written `test-123-v1/v2/v3` tags that don't exist in GHCR, so ArgoCD on main would deploy ImagePullBackOff pods.

### Kubernetes
- kubeconform strict 12/12 valid, kustomize builds. Hardened pods, a meaningful initContainer that validates mounted config, a deny-by-default NetworkPolicy, ResourceQuota, and an empty Secret mounted as a volume.
- `__pycache__` .pyc files and a junk file named 'tatus --short' are still committed.

### Observability
- Only a resource-tuned kube-prometheus-stack values file. No dashboard, logs or alerts.

## Automated tool results

Run on the default branch. hadolint, actionlint, kubeconform (`-strict`), kube-linter, kustomize, promtool, shellcheck, gitleaks over full git history, and checkov. 'Replay' runs the tests from the repo root.

| Tool | Target | Result |
|---|---|---|
| hadolint | `Dockerfile` | clean |
| actionlint | `.github/workflows/pr-validation.yml` | clean |
| actionlint | `.github/workflows/release.yml` | property "meta" is not defined in object type {image: {conclusion: string; outcome: string; outputs: {string => string}}} [expression] (+1 shellcheck notes) |
| kubeconform | `all manifests` | 14 resources found in 10 files - Valid: 12, Invalid: 0, Errors: 0, Skipped: 2 |
| kube-linter | `all manifests` | no findings |
| kustomize build | `k8s` | OK |
| gitleaks (full history) | `repo` | no leaks |
| checkov | `Dockerfile, K8s, GHA` | 9 failed checks (kubernetes 9) |
| pytest replay | `repo root` | 6 passed in 0.58s |

## Checkpoint scorecard

**Task 1: Docker and secure CI/CD**: Dockerfile (minimal base, non-root user) 🟢 · Branch protection (PR-only changes to main) 🟡 · PR pipeline: build the image 🟢 · PR pipeline: lint (Flake8) 🟢 · PR pipeline: SAST (B104 ignored) 🟢 · PR pipeline: secret scanning (optional) 🟢 · PR pipeline: unit tests (pytest) 🟢 · PR pipeline: image vulnerability scan 🟢 · Release pipeline: build and push to a registry 🟡 · Release pipeline: auto-update manifest image tags 🟡

**Task 2: GitOps with ArgoCD**: ArgoCD installed and syncing the repo 🟡 · Three Deployments behind one shared Service 🟢 · Requests set and memory limit of 400Mi 🟢 · Namespaces, ConfigMaps and Secrets 🟢 · initContainers 🟢 · RBAC (ServiceAccount, Role, RoleBinding) 🟢 · NetworkPolicies 🟢 · ResourceQuotas 🟢

**Task 3: Observability and alerting**: Prometheus and Grafana via Helm 🟡 · Dashboard: node and per-pod CPU/memory 🔴 · Log aggregation (Loki, EFK or similar) 🔴 · Grafana alerts: health check and OOMKilled 🔴 · Alert routing to Slack or email (bonus) 🔴

**Documentation**: Setup, architecture and reproduction steps 🔴

## AI usage and originality evidence

| Signal | Where | What we saw | Points to |
|---|---|---|---|
| Debug trail | `git log 17 Oct` | 'fix trivy' x3, 'k8s non root issue fix', 'increase memory quota'. | Against AI |
| Untested refactor | `release.yml` | A broken step reference that the first run would expose. Suggests a generated snippet pasted without a run. | Supports AI (weak) |

**AI read:** Mostly hands-on. The 24 Oct change looks like an assistant-written snippet that was never run.

**Originality:** His original regex tag updater (17 Oct, 12:15 IST) reappeared almost verbatim in Nikhil's repo that evening. Amit is the likely source.

## Verification questions for a screening call

1. What does steps.meta.outputs.version resolve to in your release job?
2. Which tag does the pipeline push, and which tag do the manifests ask for?
3. Why commit test-123 tags to main?

## What would move them up

- Fix the tag step (use steps.image.outputs.tag, lowercase the repo, push per-version tags).
- Restore real tags in the manifests and go back to PRs for main.
- Start Task 3 for real.
