# Vinod Dama: Internal Technical Assessment (26 Oct 2026)

> **INTERNAL.** Second, deeper review. Includes AI-use, originality and placement guidance inferred from repository evidence and tool output. Confirm in conversation before acting on it.

**Repo:** [vinod-dama/Scaler-DevOps-SRE](https://github.com/vinod-dama/Scaler-DevOps-SRE)  
**Email:** vinod.dama@gmail.com

**Bottom line:** The most hands-on GitOps loop in the cohort: matrix release to GHCR, automatic tag bumps, and ArgoCD auto-sync confirmed by screenshot. Security gates are weak and two manifest bugs break serving.

## Placement read

| | |
|---|---|
| **Tier** | **Strong**: Clear positive signal; put forward now (gaps noted). |
| **Effect on placement chances** | Increase (GitOps), with security gaps |
| **Checkpoints (of 24)** | 7 done, 10 partial, 7 missing |
| **AI-generation likelihood** | Low |
| **Originality** | Clear |
| **Batch / module** | DevOps Aug24 3 / MLOps (Kashish Bhagat) |
| **Tech YOE (sheet)** | 13 |
| **Contest final** | Passed |
| **Mock interview** | No / n/a |
| **Calling feedback** | Will not have the access to laptop till monday. have started working on it, but will be able to submit the link by Monday |

**Since 19 Oct:** New to the review. 100 own commits from 20 to 26 Oct, PRs up to #19 merged from develop to main, plus 21 bot tag-update commits proving the release loop ran repeatedly.

## Activity timeline

- **Own commits:** 100 across 7 active day(s): 10-20, 10-21, 10-22, 10-23, 10-24, 10-25, 10-26
- **First / last:** 2026-10-20 08:10 / 2026-10-26 15:00
- **PR merges:** 10 | **bot commits:** 21 | **branches:** develop, main
- **Largest single commit:** +351 lines in 1 files (2026-10-23, "helm & argocd installation")

<details><summary>Recent commits (newest last)</summary>

- `17600c9` 10-25 10:23 +3/-3 image path update
- `de29ac9` 10-25 10:28 +0/-0 image path update in deployment files
- `c3462b4` 10-25 10:56 +1/-0 Fix: image compatibility issue between amd and arm
- `ad92806` 10-25 10:57 +0/-0 image compatibility  for platforms: linux/amd64,linux/arm64
- `d33066f` 10-25 13:09 +5/-3 replicas and ArgoCD working model
- `f289963` 10-25 13:12 +1/-1 screenshot for ArgoCD
- `c1e0fdc` 10-25 13:14 +2/-0 Image of ArgoCD
- `27eeeaf` 10-25 13:16 +2/-1 update on readme.md
- `d75dc88` 10-25 13:32 +3/-3 Update readme for ArgoCD and Task 3 status
- `6e30a27` 10-26 11:47 +21/-0 annotations for promethus on Deployments and resourceQuota addition on namespace
- `94d675d` 10-26 11:48 +0/-0 annotations for prometheus on Deployments and resourceQuota addition on namespace - Merge 
- `0966a5d` 10-26 13:08 +30/-0 monitoring cluster-rbacs
- `20b5210` 10-26 13:08 +0/-0 monitoring cluster-rbacs
- `1ccf111` 10-26 14:59 +56/-0 granafa dashboard
- `8542202` 10-26 15:00 +0/-0 granafa dashboard building

</details>

## Technical deep dive

### Dockerfile
- Two-stage build (venv in builder, slim runtime), non-root `appuser`, EXPOSE 8080.
- **HEALTHCHECK calls `/health`, but the app only serves `/healthcheck` and returns 404 for everything else.** `urlopen` raises on 404, so Docker marks every container unhealthy.
- requirements.txt installs Flask, requests and pytest, none of which the app imports, and `build-essential` is pulled into the builder. That adds attack surface and Trivy noise for nothing. hadolint: DL3008, DL3013, DL3042, DL3059.
- No fixed UID (`useradd -r`), so Kubernetes `runAsUser` can't be pinned reliably.

### PR pipeline
- Triggers on push to develop and PRs to main. Runs Flake8, pytest, Buildx build and Trivy.
- Flake8 only selects fatal classes (E9, F63, F7, F82) and the second pass is `--exit-zero`, so style never gates.
- **No SAST and no secret scanning at all.** Trivy has `exit-code: '0'`, so the image scan never fails a PR. In a DevSecOps brief this is the weakest security gate among the learners who reached Task 2.

### Release pipeline
- On merged PR (and push to develop), a v1/v2/v3 matrix builds multi-arch (amd64 and arm64) images to GHCR tagged `<version>-<run_number>`, then sed-rewrites each Deployment and pushes with a pull-rebase retry loop.
- Three matrix jobs each commit and push to the same branch, which is a self-made race. He noticed and added retry logic, which shows real troubleshooting, but the clean fix is one job that updates all three after the matrix finishes.
- Images are identical across v1/v2/v3. His ArgoCD screenshot shows `devops-sre-app-v1` in ImagePullBackOff, likely a tag that was written before its image existed.

### Kubernetes
- kubeconform strict: 13/13 valid. Namespace, ConfigMap, ServiceAccount, Role/RoleBinding, ResourceQuota, three Deployments (2 replicas each, 128Mi/400Mi, httpGet probes on `/healthcheck`).
- **The Service selects `name: devops-sre-app` but the pods are labelled `app: devops-sre-app`, so the Service has zero endpoints** (kube-linter: dangling-service). No traffic reaches any version.
- The Role lets the app's ServiceAccount create, update and delete Deployments, Pods, ConfigMaps and Secrets. That is the opposite of least privilege for a pod that needs no API access.
- No Secret, no initContainer, no NetworkPolicy. kube-linter: run-as-non-root and no-read-only-root-fs on all three, and checkov shows 39 failed hardening checks.
- A cluster-wide ClusterRoleBinding grants the `default` ServiceAccount in monitoring read on nodes, pods and services, which is wider than needed.

### GitOps
- ArgoCD installed via Helm. The screenshot shows auto-sync enabled, synced to the bot commit 'update image tag for v3 to v3-38'. The Application manifest itself is not committed, so the GitOps config isn't reproducible from the repo.
- `get_helm.sh` (the upstream Helm installer) is committed under `.github/workflows/`, which is the wrong place for it.

### Observability
- Started 26 Oct: prometheus.io scrape annotations, monitoring ClusterRole, and a Grafana dashboard ConfigMap with sidecar-discovery label (working set memory, CPU).
- The scrape annotations point at port 8080, but the app has no /metrics endpoint, so those targets will fail. Node and pod metrics must come from cAdvisor and kube-state-metrics instead.
- No log stack, no alerts yet.

## Automated tool results

Run on the default branch. hadolint, actionlint, kubeconform (`-strict`), kube-linter, kustomize, promtool, shellcheck, gitleaks over full git history, and checkov. 'Replay' runs the tests from the repo root.

| Tool | Target | Result |
|---|---|---|
| hadolint | `Dockerfile` | 4 findings: DL3008, DL3013, DL3042, DL3059 |
| actionlint | `.github/workflows/ci-build+validate+scan-workflow.yaml` | clean |
| actionlint | `.github/workflows/ci-release+push-workflow.yaml` | 2 shellcheck style notes only |
| kubeconform | `all manifests` | 13 resources found in 11 files - Valid: 13, Invalid: 0, Errors: 0, Skipped: 0 |
| kube-linter | `all manifests` | no-anti-affinity x3, no-read-only-root-fs x3, run-as-non-root x3, dangling-service x1 |
| gitleaks (full history) | `repo` | no leaks |
| checkov | `Dockerfile, K8s, GHA` | 39 failed checks (kubernetes 37, github_actions 2) |
| pytest replay | `repo root` | 6 passed in 0.58s |

## Checkpoint scorecard

**Task 1: Docker and secure CI/CD**: Dockerfile (minimal base, non-root user) 🟡 · Branch protection (PR-only changes to main) 🟡 · PR pipeline: build the image 🟢 · PR pipeline: lint (Flake8) 🟡 · PR pipeline: SAST (B104 ignored) 🔴 · PR pipeline: secret scanning (optional) 🔴 · PR pipeline: unit tests (pytest) 🟢 · PR pipeline: image vulnerability scan 🟡 · Release pipeline: build and push to a registry 🟢 · Release pipeline: auto-update manifest image tags 🟢

**Task 2: GitOps with ArgoCD**: ArgoCD installed and syncing the repo 🟢 · Three Deployments behind one shared Service 🟡 · Requests set and memory limit of 400Mi 🟢 · Namespaces, ConfigMaps and Secrets 🟡 · initContainers 🔴 · RBAC (ServiceAccount, Role, RoleBinding) 🟡 · NetworkPolicies 🔴 · ResourceQuotas 🟢

**Task 3: Observability and alerting**: Prometheus and Grafana via Helm 🟡 · Dashboard: node and per-pod CPU/memory 🟡 · Log aggregation (Loki, EFK or similar) 🔴 · Grafana alerts: health check and OOMKilled 🔴 · Alert routing to Slack or email (bonus) 🔴

**Documentation**: Setup, architecture and reproduction steps 🟡

## AI usage and originality evidence

| Signal | Where | What we saw | Points to |
|---|---|---|---|
| 100 small commits with typos | `git log` | 'Github actions workflow doe build-validate-and-scan', 'syntax with extra closing brace', 'done block sitting outside of the for block'. | Against AI |
| Iterative bug hunting | `release workflow` | Race condition found and patched with pull-rebase and retry; arm64 compatibility fix. | Against AI |
| Tutorial-style Dockerfile comments | `Dockerfile` | Verbose explanatory comments, possibly from a guide or assistant. | Supports AI (weak) |
| Live screenshots | `images/` | ArgoCD install and app tree with real pod states including a failing pod. | Against AI |

**AI read:** Hands-on and self-driven. If an assistant helped, it was in small pieces that he ran and fixed.

**Originality:** No overlap with other learners.

## Verification questions for a screening call

1. kubectl get endpoints for devops-sre-service. What does it show, and why?
2. Your HEALTHCHECK hits /health. What does docker ps show for these containers?
3. Why does your Role need create and delete on Secrets?
4. Your v1 pod is in ImagePullBackOff in the screenshot. What caused it?
5. Trivy exit-code is 0. Is your PR pipeline a security gate?

## What would move them up

- Fix the Service selector and the HEALTHCHECK path.
- Add Bandit and Gitleaks, and set Trivy to exit-code 1.
- Cut the Role to read-only or none.
- Commit the ArgoCD Application manifest.
- Add Secret, initContainer and NetworkPolicy, then Loki and the two Grafana alerts.
