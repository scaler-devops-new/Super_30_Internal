# Nashit Ahmad: Internal Technical Assessment (26 Oct 2026)

> **INTERNAL.** Second, deeper review. Includes AI-use, originality and placement guidance inferred from repository evidence and tool output. Confirm in conversation before acting on it.

**Repo:** [cloudnash/DevSecOps-and-Site-Reliability-Engineering-SRE-Project](https://github.com/cloudnash/DevSecOps-and-Site-Reliability-Engineering-SRE-Project)  
**Email:** nashitakerfeldt@gmail.com

**Bottom line:** Committed unresolved git merge-conflict markers into her Deployment manifest, so Task 2 can't be applied at all.

## Placement read

| | |
|---|---|
| **Tier** | **Early**: Too little working output to judge, or stalled. |
| **Effect on placement chances** | Decrease |
| **Checkpoints (of 24)** | 7 done, 8 partial, 9 missing |
| **AI-generation likelihood** | Medium |
| **Originality** | Clear |
| **Batch / module** | DevOps Aug25 / AWS 2 (Raham Shaik) |
| **Tech YOE (sheet)** | 1.92 |
| **Contest final** | Passed |
| **Mock interview** | completed / Hire |
| **Calling feedback** | DNP |

**Since 19 Oct:** On 24 Oct added three Deployments and an enterprise.yaml, fixed the Dockerfile COPY path, and edited CI paths, all directly on main.

## Activity timeline

- **Own commits:** 5 across 2 active day(s): 10-17, 10-24
- **First / last:** 2026-10-17 15:23 / 2026-10-24 16:22
- **PR merges:** 0 | **bot commits:** 4 | **branches:** main
- **Largest single commit:** +251 lines in 3 files (2026-10-17, "Initial commit: Added starter python app")

<details><summary>Recent commits (newest last)</summary>

- `d7ccb55` 10-17 15:23 +251/-0 Initial commit: Added starter python app
- `d765dd1` 10-17 17:15 +120/-0 Added Dockerfile and pipelines
- `0185d2b` 10-24 10:30 +186/-1 Added enterprise manifests
- `115b8f8` 10-24 16:21 +1/-1 Update Dockerfile
- `5911291` 10-24 16:22 +3/-3 Update paths for linting and testing in CI pipeline

</details>

## Technical deep dive

### Dockerfile
- Fixed: now `COPY app/app.py .`, so the container starts. Non-root, no fixed UID, no HEALTHCHECK.

### PR pipeline
- `bandit -r /app` scans an absolute path that doesn't exist on the runner, so SAST checks nothing.
- `pytest tests/` points at a directory that doesn't exist (it's `test/`), so pytest exits 4 and the pipeline fails.
- Flake8 only checks fatal errors, and `pip install -r requirements.txt || true` refers to a file that doesn't exist. Trivy is gating.

### Release pipeline
- Pushes a SHA tag to GHCR and sed-updates the manifest. The bot commits show it ran, but it now rewrites a file that doesn't parse.

### Kubernetes
- **`k8s/deployments.yaml` contains `<<<<<<< HEAD`, `=======` and `>>>>>>> cb24264` markers.** kubeconform: 'did not find expected comment or line break' at line 41. ArgoCD or kubectl would reject the whole file.
- All three Deployments use the same selector `app: leaky-app`. Overlapping selectors mean each Deployment's ReplicaSet can claim the others' pods, a real correctness bug beyond the conflict.
- enterprise.yaml: Namespace, ResourceQuota, ServiceAccount (no Role), ConfigMap, a plaintext Secret (`API_KEY: super-secret-key`, flagged by gitleaks), and default-deny plus allow-8080 NetworkPolicies. The initContainer just echoes.

## Automated tool results

Run on the default branch. hadolint, actionlint, kubeconform (`-strict`), kube-linter, kustomize, promtool, shellcheck, gitleaks over full git history, and checkov. 'Replay' runs the tests from the repo root.

| Tool | Target | Result |
|---|---|---|
| hadolint | `Dockerfile` | clean |
| actionlint | `.github/workflows/pr-pipeline.yaml` | clean |
| actionlint | `.github/workflows/release-pipeline.yaml` | 2 shellcheck style notes only |
| kubeconform | `all manifests` | 7 resources found in 1 file - Valid: 7, Invalid: 0, Errors: 0, Skipped: 0 |
| YAML parse | `k8s/deployments.yaml` | FAILS: while scanning a simple key |
| kube-linter | `all manifests` | no findings |
| gitleaks (full history) | `k8s/enterprise.yaml` | 1 finding(s): kubernetes-secret-yaml |
| checkov | `Dockerfile, K8s, GHA` | 2 failed checks (dockerfile 1, github_actions 1) |
| pytest replay | `repo root` | 6 passed in 0.78s |

## Checkpoint scorecard

**Task 1: Docker and secure CI/CD**: Dockerfile (minimal base, non-root user) 🟢 · Branch protection (PR-only changes to main) 🔴 · PR pipeline: build the image 🟢 · PR pipeline: lint (Flake8) 🟡 · PR pipeline: SAST (B104 ignored) 🟡 · PR pipeline: secret scanning (optional) 🔴 · PR pipeline: unit tests (pytest) 🟡 · PR pipeline: image vulnerability scan 🟢 · Release pipeline: build and push to a registry 🟢 · Release pipeline: auto-update manifest image tags 🟡

**Task 2: GitOps with ArgoCD**: ArgoCD installed and syncing the repo 🔴 · Three Deployments behind one shared Service 🟡 · Requests set and memory limit of 400Mi 🟡 · Namespaces, ConfigMaps and Secrets 🟢 · initContainers 🟡 · RBAC (ServiceAccount, Role, RoleBinding) 🟡 · NetworkPolicies 🟢 · ResourceQuotas 🟢

**Task 3: Observability and alerting**: Prometheus and Grafana via Helm 🔴 · Dashboard: node and per-pod CPU/memory 🔴 · Log aggregation (Loki, EFK or similar) 🔴 · Grafana alerts: health check and OOMKilled 🔴 · Alert routing to Slack or email (bonus) 🔴

**Documentation**: Setup, architecture and reproduction steps 🔴

## AI usage and originality evidence

| Signal | Where | What we saw | Points to |
|---|---|---|---|
| Tutorial comments | `workflows, manifests` | '# CRITICAL: Forces OOMKilled', banner comments per Deployment. | Supports AI |
| Boilerplate | `pr-pipeline.yaml` | Installs a requirements.txt that doesn't exist. | Supports AI |
| Merge conflict committed | `deployments.yaml` | Shows a local rebase that wasn't checked, a human slip. | Neutral |

**AI read:** Template or AI-derived pieces assembled without running them.

**Originality:** No overlap. Note the commit author is now 'Nashit Ahmad <fusionmad47@gmail.com>', which differs from the sheet email.

## Verification questions for a screening call

1. Open k8s/deployments.yaml. What are lines 39 to 41?
2. Why do all three Deployments share one selector?
3. Does `pytest tests/` find your tests?

## What would move them up

- Resolve the conflict and give each Deployment a unique version label.
- Fix the pytest and Bandit paths.
- Move the Secret out of git.
