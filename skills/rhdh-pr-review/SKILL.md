---
name: rhdh-pr-review
description: Test PR changes on a live RHDH cluster. Fetches CI-built images or checks out chart branches, checks cluster status (deploying if needed), deploys the full PR operator bundle/manifests or Helm chart, actively verifies the code changes by exercising affected code paths on the cluster, and closes with findings including best-practice and security assessment. Use when asked to review an rhdh-operator PR, rhdh-chart PR, test PR changes on a cluster, deploy PR images for testing, or deploy PR bundle. Also use when user mentions "operator PR", "chart PR", "helm chart PR", "review PR", or "test this PR on my cluster".
---

<cli_setup>
This skill uses the orchestrator CLI for activity tracking. **Set up first:**

```bash
RHDH=../rhdh/scripts/rhdh
```

</cli_setup>

<essential_principles>

<principle name="ensure_cluster">
Always verify the user has a running RHDH cluster with `oc` access before deploying PR bundles.
If no cluster or no RHDH instance, provision one using `redhat-developer/rhdh-test-instance` — see `rhdh-repos.md` for details on that repo's capabilities.
Don't just tell the user to set things up — do it.
</principle>

<principle name="use_pr_comment_images">
Extract image URLs from PR comments posted by CI — never construct image URLs manually.
The tag format includes PR number + commit SHA, which only CI knows.
See `references/operator-pr-images.md` for extraction commands.
</principle>

<principle name="deploy_full_bundle">
Deploy the full PR bundle/manifests, not just the operator binary image.
PR changes to CRDs, RBAC, default config, or bundle metadata are baked into the OLM bundle or install.yaml manifests — a binary-only image swap misses them and the operator may fail to reconcile.
For OLM-managed installs, replace the CatalogSource with the PR's `operator-catalog` image so OLM reinstalls the full bundle (CRDs, RBAC, CSV, deployment).
For non-OLM installs, fetch and apply the PR branch's `install.yaml` (CRDs, RBAC, ConfigMaps, Deployment) with the CI-built operator image substituted in.
Both paths preserve existing Backstage CRs, config, and Keycloak state — only the operator-side resources are replaced.
</principle>

<principle name="deploy_full_chart">
Deploy the full chart from the PR branch, not just a values override.
PR changes to templates, helpers, schema, or dependencies are baked into the chart itself — a values-only change would miss them.
Use `helm upgrade` pointing at the local chart directory from the PR branch with `--reuse-values` to preserve existing user configuration.
See `references/chart-pr-testing.md` for deployment commands.
</principle>

</essential_principles>

<intake>

## What would you like to do?

### PR Review Tasks

*For testing PR changes on a live RHDH cluster*

1. **Review rhdh-operator PR** — Deploy PR operator bundle on cluster and get review checklist
2. **Review rhdh-chart PR** — Deploy PR chart on cluster and get review checklist

**Wait for response before proceeding.**

</intake>

<routing>

### PR Review Routes

| Response | Workflow |
|----------|----------|
| 1, "operator", "rhdh-operator", a PR number + operator context | Route to `workflows/review-operator-pr.md` |
| 2, "chart", "rhdh-chart", "helm", a PR number + chart context | Route to `workflows/review-chart-pr.md` |

**To route:** Read the corresponding workflow file and follow its process.

</routing>

<reference_index>

| Reference | Purpose | Path |
|-----------|---------|------|
| shared-pr-fetch | PR context fetching (shared) | `references/shared-pr-fetch.md` |
| shared-verification | Active verification process (shared) | `references/shared-verification.md` |
| shared-findings-structure | Findings report structure (shared) | `references/shared-findings-structure.md` |
| operator-pr-images | CI image extraction and validation (operator) | `references/operator-pr-images.md` |
| chart-pr-testing | Chart CI behavior, local validation, deployment (chart) | `references/chart-pr-testing.md` |
| github-reference | gh CLI patterns, PR queries | `../rhdh/references/github-reference.md` (if unavailable, use standard `gh` CLI patterns) |
| rhdh-repos | RHDH ecosystem repository map | `../rhdh/references/rhdh-repos.md` (if unavailable, see CONTEXT.md for repo list) |

</reference_index>

<skills_index>

| Skill | Purpose | Path |
|-------|---------|------|
| rhdh | Orchestrator, environment status, activity tracking | `../rhdh/SKILL.md` |

</skills_index>

<success_criteria>

See the corresponding workflow's `<success_criteria>` for the full checklist:
- Operator PRs: `workflows/review-operator-pr.md`
- Chart PRs: `workflows/review-chart-pr.md`

</success_criteria>
