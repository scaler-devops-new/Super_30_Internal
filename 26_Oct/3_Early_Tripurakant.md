# Tripurakant: Internal Technical Assessment (26 Oct 2026)

> **INTERNAL.** Second, deeper review. Includes AI-use, originality and placement guidance inferred from repository evidence and tool output. Confirm in conversation before acting on it.

**Repo:** [tripura-kant/gitops-devsecops-pipeline](https://github.com/tripura-kant/gitops-devsecops-pipeline)  
**Email:** tripurakant@gmail.com

**Bottom line:** A workflow that GitHub will refuse to run, and a memory limit deliberately raised away from the brief's 400Mi.

## Placement read

| | |
|---|---|
| **Tier** | **Early**: Too little working output to judge, or stalled. |
| **Effect on placement chances** | Slight decrease |
| **Checkpoints (of 24)** | 1 done, 8 partial, 15 missing |
| **AI-generation likelihood** | Medium |
| **Originality** | Clear |
| **Batch / module** | DevOps Aug24 2 / MLOps (Kashish Bhagat) |
| **Tech YOE (sheet)** | 7.42 |
| **Contest final** | Passed |
| **Mock interview** | completed / Maybe |
| **Calling feedback** | n/a |

**Since 19 Oct:** On 21 Oct added a CI/CD workflow and changed the Deployment. The memory limit went up to 700Mi, further from the brief.

## Activity timeline

- **Own commits:** 14 across 3 active day(s): 10-14, 10-15, 10-21
- **First / last:** 2026-10-14 19:53 / 2026-10-21 12:26
- **PR merges:** 0 | **bot commits:** 0 | **branches:** add-ci, main
- **Largest single commit:** +95 lines in 1 files (2026-10-21, "Add CI/CD GitHub Actions workflow")

<details><summary>Recent commits (newest last)</summary>

- `ceaa43b` 10-14 19:53 +80/-0 first commit app.py
- `d18e103` 10-14 22:37 +46/-0 added dockerfile
- `5b0834a` 10-14 22:52 +2/-2 update dockerfile
- `8faa21d` 10-14 22:52 +2/-2 update dockerfile
- `9e63d8e` 10-14 22:56 +1/-0 Create README.md
- `9235cf7` 10-15 22:45 +68/-5 update dockerfile
- `b4dbf95` 10-15 22:45 +68/-5 update dockerfile
- `7473cdd` 10-21 10:11 +7/-7 update dockerfile
- `9ff322c` 10-21 11:01 +95/-0 Add CI/CD GitHub Actions workflow
- `cf0fc47` 10-21 11:10 +21/-13 Fix linting issues and bandit warnings
- `1fcaba5` 10-21 11:12 +1/-0 Make k8s-validate job conditional on KUBECONFIG secret
- `8f7ed92` 10-21 12:26 +4/-3 Update deployment: local image, increased memory, runAsUser

</details>

## Technical deep dive

### Dockerfile
- python:3.12-alpine, UID 10001, `USER` before `COPY`, so files are root-owned but readable. Fine. No HEALTHCHECK.

### CI/CD
- **`if: ${{ secrets.KUBECONFIG != '' }}` at job level is invalid (actionlint: context 'secrets' not allowed here).** GitHub rejects the whole workflow file, so no job runs at all.
- Even if it ran: no image scan, Bandit without B104 handling (it passes only because app.py was edited), and the release pushes to GHCR on any push to main or add-ci, with no PR gating.

### Kubernetes
- One Deployment with httpGet probes and `runAsNonRoot`. **Requests 512Mi and limit 700Mi** ('increased memory'), against the explicit 400Mi.
- At 700Mi the leak (about 3.3 MB/s after 100 MB) OOMs at around 3 minutes instead of around 90 seconds. The point of the exercise was the 400Mi kill.

### Hygiene
- `.flake8.py` and `pytest.ini.py` are still misnamed, and `.idea/` is committed. A stray `pr-pipeline.yml` sits at the repo root, outside `.github/workflows`, so it never runs.

## Automated tool results

Run on the default branch. hadolint, actionlint, kubeconform (`-strict`), kube-linter, kustomize, promtool, shellcheck, gitleaks over full git history, and checkov. 'Replay' runs the tests from the repo root.

| Tool | Target | Result |
|---|---|---|
| hadolint | `Dockerfile` | clean |
| actionlint | `.github/workflows/ci-cd.yml` | context "secrets" is not allowed here. available contexts are "github", "inputs", "needs", "vars". see https://docs.github.com/en/actions/le |
| kubeconform | `all manifests` | 1 resource found in 1 file - Valid: 1, Invalid: 0, Errors: 0, Skipped: 0 |
| kube-linter | `all manifests` | no-read-only-root-fs x1 |
| gitleaks (full history) | `repo` | no leaks |
| checkov | `Dockerfile, K8s, GHA` | 13 failed checks (kubernetes 11, dockerfile 1, github_actions 1) |
| pytest replay | `repo root` | 2 passed in 0.55s |

## Checkpoint scorecard

**Task 1: Docker and secure CI/CD**: Dockerfile (minimal base, non-root user) 🟢 · Branch protection (PR-only changes to main) 🔴 · PR pipeline: build the image 🟡 · PR pipeline: lint (Flake8) 🟡 · PR pipeline: SAST (B104 ignored) 🟡 · PR pipeline: secret scanning (optional) 🔴 · PR pipeline: unit tests (pytest) 🟡 · PR pipeline: image vulnerability scan 🔴 · Release pipeline: build and push to a registry 🟡 · Release pipeline: auto-update manifest image tags 🔴

**Task 2: GitOps with ArgoCD**: ArgoCD installed and syncing the repo 🔴 · Three Deployments behind one shared Service 🟡 · Requests set and memory limit of 400Mi 🟡 · Namespaces, ConfigMaps and Secrets 🔴 · initContainers 🔴 · RBAC (ServiceAccount, Role, RoleBinding) 🔴 · NetworkPolicies 🔴 · ResourceQuotas 🔴

**Task 3: Observability and alerting**: Prometheus and Grafana via Helm 🔴 · Dashboard: node and per-pod CPU/memory 🔴 · Log aggregation (Loki, EFK or similar) 🔴 · Grafana alerts: health check and OOMKilled 🔴 · Alert routing to Slack or email (bonus) 🔴

**Documentation**: Setup, architecture and reproduction steps 🟡

## AI usage and originality evidence

| Signal | Where | What we saw | Points to |
|---|---|---|---|
| Templated workflow | `ci-cd.yml` | metadata-action tag matrix, GHA cache, azure/k8s-set-context: a common generated template. | Supports AI |
| Never-ran error | `ci-cd.yml` | An invalid secrets expression that the first push would surface. | Supports AI |

**AI read:** The workflow looks generated and was never run.

**Originality:** No overlap.

## Verification questions for a screening call

1. Open the Actions tab. Why didn't your workflow run?
2. Why raise the limit to 700Mi when the brief says 400Mi?

## What would move them up

- Fix the job-level if, or remove that job.
- Add Trivy and set 400Mi.
- Rename the config files.
