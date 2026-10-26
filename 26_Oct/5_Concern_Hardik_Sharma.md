# Hardik Sharma: Internal Technical Assessment (26 Oct 2026)

> **INTERNAL.** Second, deeper review. Includes AI-use, originality and placement guidance inferred from repository evidence and tool output. Confirm in conversation before acting on it.

**Repo:** [Hardik-sh-devops/DevOps-SRE-Scaler-lab](https://github.com/Hardik-sh-devops/DevOps-SRE-Scaler-lab)  
**Email:** hardiksharmaofficial07@gmail.com

**Bottom line:** Some genuine CI effort, but a second reuse instance: his helper scripts are Shishir's with every comment rewritten.

## Placement read

| | |
|---|---|
| **Tier** | **Concern**: Originality or ownership concern; lower priority. |
| **Effect on placement chances** | Decrease |
| **Checkpoints (of 24)** | 3 done, 4 partial, 17 missing |
| **AI-generation likelihood** | Medium |
| **Originality** | Flagged again (scripts match Shishir) |
| **Batch / module** | DevOps Oct25 / DevOps Tools 2 (Akshat Gupta) |
| **Tech YOE (sheet)** | 0 |
| **Contest final** | Passed |
| **Mock interview** | No / n/a |
| **Calling feedback** | Have started. Will submit by today evening |

**Since 19 Oct:** Ten commits from 19 to 26 Oct: a PR-check workflow and three helper scripts.

## Activity timeline

- **Own commits:** 14 across 8 active day(s): 10-16, 10-17, 10-19, 10-20, 10-21, 10-23, 10-24, 10-26
- **First / last:** 2026-10-16 13:55 / 2026-10-26 09:13
- **PR merges:** 0 | **bot commits:** 0 | **branches:** main
- **Largest single commit:** +257 lines in 4 files (2026-10-16, "Dockerfile added")

<details><summary>Recent commits (newest last)</summary>

- `7ce2d30` 10-16 13:55 +257/-0 Dockerfile added
- `d96aae2` 10-17 15:13 +1/-0 Create .github
- `6c0662e` 10-17 15:15 +0/-1 Delete .github
- `20d34cd` 10-17 15:28 +1/-0 Create pr-checks.yaml
- `878a122` 10-19 22:30 +37/-0 Update pr-checks.yaml
- `eeb2bd4` 10-20 16:52 +5/-0 Add secret scanning step to PR checks workflow
- `40877f5` 10-20 17:24 +14/-3 Modify PR checks workflow for push events
- `52046d1` 10-21 22:03 +2/-1 Enhance security testing output formats
- `ebf48a5` 10-23 20:53 +16/-0 Add script to check for vulnerabilities in reports
- `1b56fc6` 10-23 20:55 +26/-0 Add script to create and switch to a feature branch
- `b307713` 10-23 20:57 +92/-0 Add feature-push.sh script for Git automation
- `8cbf05e` 10-23 21:04 +16/-0 Enhance PR checks with vulnerability scan and PR creation
- `387e6e7` 10-24 11:37 +6/-6 Replace Flask8 with Flake8 in CI workflow
- `d843358` 10-26 09:13 +27/-7 Enhance vulnerability check script with jq validation

</details>

## Technical deep dive

### Dockerfile
- Unchanged, identical to Shishir's apart from the EXPOSE line. No HEALTHCHECK.

### PR pipeline
- Triggers on push to a branch literally named `feature` (not on PRs), then runs `gh pr create`.
- Bandit runs without `-s B104` and exits 1 on the 0.0.0.0 bind, so the job stops there. Flake8 also fails on the starter files.
- **The step `bash check_vulnerabilities.sh` runs from the repo root, but the script lives in `script/`.** It would fail with 'No such file' even if earlier steps passed.
- The jq-based report check is a nice idea, but it duplicates Trivy's own `exit-code: 1`, which already stops the job first.

## Automated tool results

Run on the default branch. hadolint, actionlint, kubeconform (`-strict`), kube-linter, kustomize, promtool, shellcheck, gitleaks over full git history, and checkov. 'Replay' runs the tests from the repo root.

| Tool | Target | Result |
|---|---|---|
| hadolint | `Dockerfile` | clean |
| actionlint | `.github/workflows/pr-checks.yaml` | clean |
| shellcheck | `script/check_vulnerabilities.sh` | 1 findings |
| shellcheck | `script/feature-push.sh` | 5 findings |
| shellcheck | `script/feature-start.sh` | 1 findings |
| gitleaks (full history) | `repo` | no leaks |
| checkov | `Dockerfile, K8s, GHA` | 1 failed checks (dockerfile 1) |
| pytest replay | `repo root` | 6 passed in 0.70s |

## Checkpoint scorecard

**Task 1: Docker and secure CI/CD**: Dockerfile (minimal base, non-root user) 🟡 · Branch protection (PR-only changes to main) 🔴 · PR pipeline: build the image 🟡 · PR pipeline: lint (Flake8) 🟡 · PR pipeline: SAST (B104 ignored) 🟡 · PR pipeline: secret scanning (optional) 🟢 · PR pipeline: unit tests (pytest) 🟢 · PR pipeline: image vulnerability scan 🟢 · Release pipeline: build and push to a registry 🔴 · Release pipeline: auto-update manifest image tags 🔴

**Task 2: GitOps with ArgoCD**: ArgoCD installed and syncing the repo 🔴 · Three Deployments behind one shared Service 🔴 · Requests set and memory limit of 400Mi 🔴 · Namespaces, ConfigMaps and Secrets 🔴 · initContainers 🔴 · RBAC (ServiceAccount, Role, RoleBinding) 🔴 · NetworkPolicies 🔴 · ResourceQuotas 🔴

**Task 3: Observability and alerting**: Prometheus and Grafana via Helm 🔴 · Dashboard: node and per-pod CPU/memory 🔴 · Log aggregation (Loki, EFK or similar) 🔴 · Grafana alerts: health check and OOMKilled 🔴 · Alert routing to Slack or email (bonus) 🔴

**Documentation**: Setup, architecture and reproduction steps 🔴

## AI usage and originality evidence

| Signal | Where | What we saw | Points to |
|---|---|---|---|
| Paraphrased copy | `script/feature-start.sh, script/feature-push.sh` | Same logic and structure as Shishir's scripts/feature.sh and push.sh (17 Oct), with every comment reworded and `git checkout -b` swapped for `git switch -c`. Shishir's usage text even names a 'feature-start.sh' file, which is Hardik's filename. | Copying (AI-reworded) |
| Real iteration | `git log` | 'Replace Flask8 with Flake8', jq validation added after a failure. | Against AI |

**AI read:** He iterated on his own workflow, but the helper scripts look like Shishir's files run through an assistant to reword them.

**Originality:** Two reuse instances now (Dockerfile on 16 Oct, scripts on 23 Oct), both from Shishir, same batch. Worth a direct conversation.

## Verification questions for a screening call

1. Where did feature-start.sh come from?
2. What happens at the check_vulnerabilities step?
3. Why does Bandit fail?

## What would move them up

- Fix the script path and B104 handling.
- Trigger on pull_request.
- Originality conversation.
