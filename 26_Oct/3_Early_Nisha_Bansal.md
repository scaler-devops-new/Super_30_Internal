# Nisha Bansal: Internal Technical Assessment (26 Oct 2026)

> **INTERNAL.** Second, deeper review. Includes AI-use, originality and placement guidance inferred from repository evidence and tool output. Confirm in conversation before acting on it.

**Repo:** [Nisha396/Devops-SRE](https://github.com/Nisha396/Devops-SRE)  
**Email:** nishabansal19@gmail.com

**Bottom line:** A sensible security stack (Ruff, CodeQL with a scoped 0.0.0.0 exclusion, Gitleaks, Trivy) and a release loop that updates a manifest, but the manifest is structurally invalid and has no resource limits.

## Placement read

| | |
|---|---|
| **Tier** | **Early**: Too little working output to judge, or stalled. |
| **Effect on placement chances** | Neutral |
| **Checkpoints (of 24)** | 7 done, 4 partial, 13 missing |
| **AI-generation likelihood** | Low |
| **Originality** | Clear |
| **Batch / module** | DevOps Dec24(1) / High Level Design (Pragy Agarwal) |
| **Tech YOE (sheet)** | 7.5 |
| **Contest final** | Passed |
| **Mock interview** | No / n/a |
| **Calling feedback** | Not yet started. Will start next week. Has 15 years od experiene, not getting opportunities as expected |

**Since 19 Oct:** New to the review. Everything was done on 23 Oct in one session (10 commits, 1 PR).

## Activity timeline

- **Own commits:** 10 across 1 active day(s): 10-23
- **First / last:** 2026-10-23 08:59 / 2026-10-23 16:02
- **PR merges:** 1 | **bot commits:** 1 | **branches:** feature/practice, main
- **Largest single commit:** +451 lines in 14 files (2026-10-23, "Committing dockerfile and ci/cd pipeline")

<details><summary>Recent commits (newest last)</summary>

- `28b0932` 10-23 08:59 +451/-251 Committing dockerfile and ci/cd pipeline
- `a8b199c` 10-23 09:22 +28/-24 Committing all the files
- `0857915` 10-23 09:54 +4/-0 updated working pipeline
- `6b869a7` 10-23 10:01 +2/-0 Added history commit to pipeline
- `533f714` 10-23 10:09 +3/-12 Updated Dockerfile
- `692399b` 10-23 10:13 +1/-1 Excluded dockerfile in linting
- `f631f0c` 10-23 10:22 +8/-8 fixed linting issues
- `c71dbfd` 10-23 10:25 +1/-1 fixed location of test_case.py file
- `31e3e64` 10-23 10:28 +1/-1 removed high scan from trivy

</details>

## Technical deep dive

### Dockerfile
- The multi-stage build is pointless: dependencies are installed in the build stage but never copied, and the runtime stage does `COPY . .`, shipping the whole repo (workflows, .idea, tests) because there's no .dockerignore. Non-root user, no HEALTHCHECK.
- requirements.txt pins Flask, requests and python-dotenv, none of which the app uses.

### PR pipeline
- Gitleaks, Ruff, CodeQL with a custom config excluding the bind-all-interfaces query (a precise way to honour the B104 rule), pytest, build, Trivy. Trivy was narrowed to CRITICAL ('removed high scan from trivy').

### Release pipeline
- On push to main, Docker Hub push with `<run>-<sha>` tag, sed on `manifests/Deployment.yaml`, commit back. The bot commit shows it ran. Docker Hub credentials are stored as secrets named `username` and `password`.

### Kubernetes
- **Deployment.yaml has broken indentation: `matchLabels:` and `app:` are siblings, and the template's `metadata:` is null.** kubeconform strict rejects it ('additionalProperties app not allowed'), and the API server would reject it too.
- No resources block, so the 400Mi requirement is unmet and the pod can consume the node.

## Automated tool results

Run on the default branch. hadolint, actionlint, kubeconform (`-strict`), kube-linter, kustomize, promtool, shellcheck, gitleaks over full git history, and checkov. 'Replay' runs the tests from the repo root.

| Tool | Target | Result |
|---|---|---|
| hadolint | `Dockerfile` | clean |
| actionlint | `.github/workflows/main-deploy.yaml` | 3 shellcheck style notes only |
| actionlint | `.github/workflows/pr-deploy.yaml` | clean |
| kubeconform | `all manifests` | 1 resource found in 1 file - Valid: 1, Invalid: 0, Errors: 0, Skipped: 0 |
| kube-linter | `all manifests` | mismatching-selector x1, no-anti-affinity x1, no-read-only-root-fs x1, run-as-non-root x1, unset-cpu-requirements x1, unset-memory-requirements x1 |
| gitleaks (full history) | `repo` | no leaks |
| checkov | `Dockerfile, K8s, GHA` | 22 failed checks (kubernetes 19, dockerfile 1, github_actions 2) |
| pytest replay | `repo root` | 6 passed in 0.72s |

## Checkpoint scorecard

**Task 1: Docker and secure CI/CD**: Dockerfile (minimal base, non-root user) 🟡 · Branch protection (PR-only changes to main) 🟡 · PR pipeline: build the image 🟢 · PR pipeline: lint (Flake8) 🟢 · PR pipeline: SAST (B104 ignored) 🟢 · PR pipeline: secret scanning (optional) 🟢 · PR pipeline: unit tests (pytest) 🟢 · PR pipeline: image vulnerability scan 🟡 · Release pipeline: build and push to a registry 🟢 · Release pipeline: auto-update manifest image tags 🟢

**Task 2: GitOps with ArgoCD**: ArgoCD installed and syncing the repo 🔴 · Three Deployments behind one shared Service 🟡 · Requests set and memory limit of 400Mi 🔴 · Namespaces, ConfigMaps and Secrets 🔴 · initContainers 🔴 · RBAC (ServiceAccount, Role, RoleBinding) 🔴 · NetworkPolicies 🔴 · ResourceQuotas 🔴

**Task 3: Observability and alerting**: Prometheus and Grafana via Helm 🔴 · Dashboard: node and per-pod CPU/memory 🔴 · Log aggregation (Loki, EFK or similar) 🔴 · Grafana alerts: health check and OOMKilled 🔴 · Alert routing to Slack or email (bonus) 🔴

**Documentation**: Setup, architecture and reproduction steps 🔴

## AI usage and originality evidence

| Signal | Where | What we saw | Points to |
|---|---|---|---|
| Human commit trail | `git log 23 Oct` | 'Committing all the files', 'fixed location of test_case.py file', 'removed high scan from trivy'. | Against AI |

**AI read:** Hands-on.

**Originality:** No overlap.

## Verification questions for a screening call

1. Apply your Deployment to kind. What error do you get?
2. What does your runtime image contain?
3. Why remove HIGH from Trivy?

## What would move them up

- Fix the Deployment YAML and add 400Mi limits.
- Fix the Dockerfile (copy only app.py).
- Three Deployments, Service, ArgoCD.
