# Kiruthik Raj K: Internal Technical Assessment (26 Oct 2026)

> **INTERNAL.** Second, deeper review. Includes AI-use, originality and placement guidance inferred from repository evidence and tool output. Confirm in conversation before acting on it.

**Repo:** [Kiruthik25/DevSecOps-Assessment](https://github.com/Kiruthik25/DevSecOps-Assessment)  
**Email:** connect2kiruthik@gmail.com

**Bottom line:** The only learner with end-to-end proof: green PR and release pipelines, an enforced ruleset, ArgoCD synced, dashboards, EFK, and a real OOMKilled alert email.

## Placement read

| | |
|---|---|
| **Tier** | **Strong**: Clear positive signal; put forward now (gaps noted). |
| **Effect on placement chances** | Increase |
| **Checkpoints (of 24)** | 19 done, 3 partial, 2 missing |
| **AI-generation likelihood** | Medium (docs only) |
| **Originality** | Clear |
| **Batch / module** | DevOps Dec24(2) / High Level Design (Pragy Agarwal) |
| **Tech YOE (sheet)** | 0.5 |
| **Contest final** | Passed |
| **Mock interview** | No / n/a |
| **Calling feedback** | Has started working. Will submit. |

**Since 19 Oct:** Big step up. On 21 Oct fixed both pipelines (pytest now runs from test/, app.py reformatted for Flake8, Gitleaks token fixed), created a 'Main Branch Protection' ruleset (4 rules, 1 approver), and moved work through PRs #5 and #6. The release run on PR #6 is green in his screenshot, with all three matrix builds and the manifest-update PR succeeding.

## Activity timeline

- **Own commits:** 36 across 4 active day(s): 10-15, 10-16, 10-17, 10-21
- **First / last:** 2026-10-15 17:12 / 2026-10-21 19:16
- **PR merges:** 2 | **bot commits:** 0 | **branches:** Kiruthik25-patch-1, Kiruthik25-patch-2, Kiruthik25-patch-3, Kiruthik25-patch-4, automation/update-image-tags-6a8991f6cac1acea474d9fde71420162e1a55ff6, automation/update-image-tags-83cee2de462633ef734209153ad9d06b6ff48597, feature-branch, main
- **Largest single commit:** +404 lines in 13 files (2026-10-15, "first commit")

<details><summary>Recent commits (newest last)</summary>

- `0b1d89f` 10-21 11:38 +6/-51 chore(gitops): update application image tags
- `0b24b45` 10-21 13:29 +3/-3 chore(gitops): update application image tags
- `f057001` 10-21 16:06 +1/-1 Change GITHUB_TOKEN to GH_PAT for Gitleaks
- `058259c` 10-21 16:10 +1/-0 Ignore changes to README.md in CI/CD workflow
- `b6af316` 10-21 16:21 +1/-0 Add initial test message to Portection_Test.txt
- `7676921` 10-21 16:31 +1/-1 Update Trivy action version in PR validation workflow
- `f535fce` 10-21 16:34 +1/-1 Update Trivy action version in PR validation workflow
- `0af277c` 10-21 17:03 +14/-10 Refactor logging configuration and formatting
- `e7b9eef` 10-21 17:07 +1/-1 Update Trivy action version in PR validation workflow
- `83cee2d` 10-21 17:08 +14/-10 Refactor logging configuration and improve formatting
- `1a4168b` 10-21 18:37 +1/-1 Change working directory for unit tests to 'test'
- `b7983e0` 10-21 18:40 +2/-0 Update pr-validation.yaml
- `3243a8f` 10-21 18:56 +1/-0 Add Pr-test.txt for testing PR
- `45052fc` 10-21 19:15 +3/-0 Update paths-ignore in CI/CD workflow
- `3a8d549` 10-21 19:16 +0/-0 Add files via upload

</details>

## Technical deep dive

### Dockerfile
- python:3.12-slim, fixed UID/GID 10001, nologin shell, `COPY --chown`, HEALTHCHECK on `/healthcheck` with a 2s timeout. hadolint clean.
- Slim rather than Alpine or distroless, so the image is roughly 3x the minimum. Acceptable trade-off for glibc compatibility, but he should be able to say why.

### PR pipeline
- Flake8, pytest, Bandit (`-s B104`), Gitleaks, Buildx build and Trivy (HIGH/CRITICAL, `exit-code: 1`, ignore-unfixed) in one job. Replayed locally on the current code: Flake8 0 issues, Bandit 0, pytest 6/6.
- `pip-audit` is installed but never run, so dependency scanning is implied but absent.
- Everything runs serially in one job, so a lint failure hides scan results. Splitting into jobs would give faster, clearer feedback.

### Release pipeline
- Push to main builds a v1/v2/v3 matrix to Docker Hub with `<version>-<sha12>` tags, then opens a manifest-update PR via `peter-evans/create-pull-request`. Screenshot of run #17 shows 4/4 jobs green.
- All three versions are the same build context, so v1/v2/v3 are identical images under different tags. That meets the letter of the brief, not the intent of 'multiple versions'.
- `paths-ignore` excludes `.github/**` and README, so workflow-only changes never get a release run to validate them.

### Kubernetes
- Kustomize base builds cleanly (`kustomize build` OK). kubeconform strict: 11 valid, 5 CRDs skipped. kube-linter: no findings, the cleanest manifests in the cohort.
- Three Deployments behind one ClusterIP Service, 128Mi request and 400Mi limit, read-only root FS, all capabilities dropped, seccomp RuntimeDefault, no SA token automount.
- `networkpolicy.yaml` is still a 0-byte file and is not in `kustomization.yaml`. No Secret and no initContainer, a week after first flagged.
- Probes are `tcpSocket`. They pass as long as the port is open, so a process that stops answering HTTP would still count as healthy, which weakens the health-check alert.

### GitOps
- ArgoCD Application tracks `app/deploy/base` at HEAD. The screenshot shows Synced and Healthy. Auto-sync is off and the destination namespace is `default` while the manifests hard-code `memory-app`.

### Observability
- kube-prometheus-stack with Grafana SMTP, EFK via the ECK operator plus Fluent Bit, and two PrometheusRules (promtool: 2 rules valid). The OOMKilled rule correlates `last_terminated_reason` with a restart increase, which avoids stale-reason false positives. A good detail.
- Alerting runs through Alertmanager, not Grafana-managed alerts as the brief asks.

### Security and hygiene
- gitleaks over full history: no leaks. checkov: no Kubernetes or Dockerfile failures flagged.
- Two commits landed directly on main *after* the ruleset was created (21 Oct 19:15 and 19:16), so either admin bypass is allowed or the ruleset was not yet active.

## Automated tool results

Run on the default branch. hadolint, actionlint, kubeconform (`-strict`), kube-linter, kustomize, promtool, shellcheck, gitleaks over full git history, and checkov. 'Replay' runs the tests from the repo root.

| Tool | Target | Result |
|---|---|---|
| hadolint | `app/Dockerfile` | clean |
| actionlint | `.github/workflows/ci-cd.yaml` | clean |
| actionlint | `.github/workflows/pr-validation.yaml` | clean |
| kubeconform | `all manifests` | 16 resources found in 15 files - Valid: 11, Invalid: 0, Errors: 0, Skipped: 5 |
| kube-linter | `all manifests` | no findings |
| kustomize build | `app/deploy/base` | OK |
| promtool | `altering/alerting-rules.yaml` | 2 rules valid |
| gitleaks (full history) | `repo` | no leaks |
| checkov | `Dockerfile, K8s, GHA` | 9 failed checks (kubernetes 9) |
| pytest replay | `repo root` | 6 passed in 0.71s |

## Checkpoint scorecard

**Task 1: Docker and secure CI/CD**: Dockerfile (minimal base, non-root user) 🟢 · Branch protection (PR-only changes to main) 🟢 · PR pipeline: build the image 🟢 · PR pipeline: lint (Flake8) 🟢 · PR pipeline: SAST (B104 ignored) 🟢 · PR pipeline: secret scanning (optional) 🟢 · PR pipeline: unit tests (pytest) 🟢 · PR pipeline: image vulnerability scan 🟢 · Release pipeline: build and push to a registry 🟢 · Release pipeline: auto-update manifest image tags 🟢

**Task 2: GitOps with ArgoCD**: ArgoCD installed and syncing the repo 🟡 · Three Deployments behind one shared Service 🟢 · Requests set and memory limit of 400Mi 🟢 · Namespaces, ConfigMaps and Secrets 🟡 · initContainers 🔴 · RBAC (ServiceAccount, Role, RoleBinding) 🟢 · NetworkPolicies 🔴 · ResourceQuotas 🟢

**Task 3: Observability and alerting**: Prometheus and Grafana via Helm 🟢 · Dashboard: node and per-pod CPU/memory 🟢 · Log aggregation (Loki, EFK or similar) 🟢 · Grafana alerts: health check and OOMKilled 🟡 · Alert routing to Slack or email (bonus) 🟢

**Documentation**: Setup, architecture and reproduction steps 🟢

## AI usage and originality evidence

| Signal | Where | What we saw | Points to |
|---|---|---|---|
| README overclaims | `readme.md` | Still lists Secrets, NetworkPolicies and init containers, none of which exist. | Supports AI |
| Docs reference phantom files | `grafana/README.md, altering/readme.md` | Names files and folders not present in the repo. | Supports AI |
| Real debugging trail | `git log 21 Oct` | Trivy action version bumped three times, Gitleaks token swapped, pytest dir fixed: classic run-fail-fix. | Against AI |
| Screenshots from his own machine | `Secure Pipeline/, ArgoCd/, altering/` | Green release run, ruleset page, ArgoCD tree, OOMKilled email. | Against AI |

**AI read:** AI almost certainly wrote the docs, but the engineering was run and debugged by him. Treat the docs as a coaching point, not an integrity issue.

**Originality:** No overlap with other learners.

## Verification questions for a screening call

1. Your probes are tcpSocket. What failure would they miss that httpGet on /healthcheck would catch?
2. networkpolicy.yaml is empty but the README says you implemented NetworkPolicies. Walk me through what it should contain.
3. Two commits hit main after your ruleset was created. How did they get past it?
4. Why do v1, v2 and v3 differ if they are built from the same context?
5. Explain why your OOMKilled rule joins on restarts_total.

## What would move them up

- Fill NetworkPolicy, add a Secret and an initContainer.
- Switch probes to httpGet.
- Enable ArgoCD auto-sync into the right namespace.
- Rebuild the two alerts as Grafana-managed rules.
- Fix the README so it only claims what exists.
