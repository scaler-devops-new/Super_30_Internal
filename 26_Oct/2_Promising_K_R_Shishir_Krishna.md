# K R Shishir Krishna: Internal Technical Assessment (26 Oct 2026)

> **INTERNAL.** Second, deeper review. Includes AI-use, originality and placement guidance inferred from repository evidence and tool output. Confirm in conversation before acting on it.

**Repo:** [shishir-krishna-101/DevOps-SRE](https://github.com/shishir-krishna-101/DevOps-SRE)  
**Email:** shishirkrishna18@gmail.com

**Bottom line:** One of the richest security gates in the cohort, built and debugged live over an evening. No release pipeline, and dev tools ship in the production image.

## Placement read

| | |
|---|---|
| **Tier** | **Promising**: Real skill visible; one more push or one live check. |
| **Effect on placement chances** | Increase |
| **Checkpoints (of 24)** | 6 done, 2 partial, 16 missing |
| **AI-generation likelihood** | Low |
| **Originality** | Source of copied files (Harshal, Hardik) |
| **Batch / module** | DevOps Oct25 / DevOps Tools 2 (Akshat Gupta) |
| **Tech YOE (sheet)** | 4.5 |
| **Contest final** | Passed |
| **Mock interview** | No / n/a |
| **Calling feedback** | Submitted. share gem tool |

**Since 19 Oct:** Real progress on 24 Oct: a 'Feature Security Gate' workflow with Ruff, pytest, Gitleaks, CodeQL, pip-audit, build and Trivy, which auto-opens a PR to main when green. Seven PRs merged, with a run of Trivy fixes.

## Activity timeline

- **Own commits:** 25 across 4 active day(s): 10-15, 10-16, 10-17, 10-24
- **First / last:** 2026-10-15 21:29 / 2026-10-24 23:24
- **PR merges:** 7 | **bot commits:** 0 | **branches:** feature/feature1-script, feature/security_pipeline, feature/trivy-fix, main
- **Largest single commit:** +251 lines in 3 files (2026-10-15, "Adding the project")

<details><summary>Recent commits (newest last)</summary>

- `e125638` 10-17 02:40 +15/-0 feature script added
- `bf9e478` 10-17 02:49 +86/-0 adding automation git scripts
- `c843055` 10-24 20:19 +129/-0 adding security pipeline
- `ad6d2d8` 10-24 20:34 +0/-33 adding security pipeline
- `8fc3080` 10-24 20:39 +1/-1 change trivy version
- `f3e2442` 10-24 20:46 +1/-1 fixing the typo
- `84d9c9d` 10-24 20:52 +0/-1 remove cache
- `0d29397` 10-24 21:51 +4/-4 fixing the python code - linting
- `19b1748` 10-24 21:55 +18/-17 Fix broad exception handling flagged by Ruff
- `6f3653c` 10-24 22:08 +4/-0 trivy-fixes
- `1e4f04d` 10-24 22:22 +9/-7 trivy fix
- `b605698` 10-24 22:24 +1/-1 ignore changes
- `6d7af7b` 10-24 22:26 +3/-1 req
- `fee4af9` 10-24 22:28 +4/-4 update gitignore
- `6fb2e25` 10-24 23:22 +3/-3 version update

</details>

## Technical deep dive

### Dockerfile
- python:3.13-alpine, non-root. Still `EXPOSE 80` while the app listens on 8080.
- **`pip install -r requirements.txt` in the runtime image installs pytest, ruff and pip-audit into production.** The app has no dependencies, so the image grows and the Trivy surface widens. The pins on msgpack, setuptools and urllib3 look like CVE remediation for exactly that self-inflicted surface.

### PR pipeline
- Triggers on push to `feature/**`, runs the gate, then `gh pr create`. An inverted but valid design: the PR only exists once checks pass. Nothing re-runs on the PR itself, though, so a PR from any other branch isn't gated.
- CodeQL plus pip-audit plus Gitleaks plus Trivy HIGH/CRITICAL `exit-code 1` is a thorough stack. Replay: Ruff passes after his 'broad exception' fix, and pytest 6/6.

### Release, Kubernetes, observability
- Not started.

## Automated tool results

Run on the default branch. hadolint, actionlint, kubeconform (`-strict`), kube-linter, kustomize, promtool, shellcheck, gitleaks over full git history, and checkov. 'Replay' runs the tests from the repo root.

| Tool | Target | Result |
|---|---|---|
| hadolint | `Dockerfile` | clean |
| actionlint | `.github/workflows/security.yaml` | clean |
| shellcheck | `scripts/feature.sh` | clean |
| shellcheck | `scripts/push.sh` | 5 findings |
| gitleaks (full history) | `repo` | no leaks |
| checkov | `Dockerfile, K8s, GHA` | 1 failed checks (dockerfile 1) |
| pytest replay | `repo root` | 6 passed in 0.61s |

## Checkpoint scorecard

**Task 1: Docker and secure CI/CD**: Dockerfile (minimal base, non-root user) 🟡 · Branch protection (PR-only changes to main) 🟡 · PR pipeline: build the image 🟢 · PR pipeline: lint (Flake8) 🟢 · PR pipeline: SAST (B104 ignored) 🟢 · PR pipeline: secret scanning (optional) 🟢 · PR pipeline: unit tests (pytest) 🟢 · PR pipeline: image vulnerability scan 🟢 · Release pipeline: build and push to a registry 🔴 · Release pipeline: auto-update manifest image tags 🔴

**Task 2: GitOps with ArgoCD**: ArgoCD installed and syncing the repo 🔴 · Three Deployments behind one shared Service 🔴 · Requests set and memory limit of 400Mi 🔴 · Namespaces, ConfigMaps and Secrets 🔴 · initContainers 🔴 · RBAC (ServiceAccount, Role, RoleBinding) 🔴 · NetworkPolicies 🔴 · ResourceQuotas 🔴

**Task 3: Observability and alerting**: Prometheus and Grafana via Helm 🔴 · Dashboard: node and per-pod CPU/memory 🔴 · Log aggregation (Loki, EFK or similar) 🔴 · Grafana alerts: health check and OOMKilled 🔴 · Alert routing to Slack or email (bonus) 🔴

**Documentation**: Setup, architecture and reproduction steps 🔴

## AI usage and originality evidence

| Signal | Where | What we saw | Points to |
|---|---|---|---|
| Fix-forward trail | `git log 24 Oct` | 'trivy-fixes', 'trivy fix', 'req', 'update gitignore', 'version update' within an hour. | Against AI |

**AI read:** Hands-on.

**Originality:** His Dockerfile (16 Oct) and helper scripts (17 Oct) were reused by Harshal (Dockerfile) and Hardik (Dockerfile, plus the scripts with comments reworded on 23 Oct). All three are in the same batch. Ask whether he shared them; there is no negative signal on his own work.

## Verification questions for a screening call

1. Why are pytest and pip-audit in your runtime image?
2. If someone opens a PR from a branch not named feature/*, what checks run?
3. Did you share your scripts with batchmates?

## What would move them up

- Split dev and runtime dependencies (multi-stage or no requirements in runtime).
- Fix EXPOSE.
- Add a pull_request trigger and a release pipeline.
