# Reference: Cluster Provisioning for PR Review

How to get a running RHDH cluster when the reviewer doesn't have one. This is a thin pointer — the authoritative documentation lives in the rhdh-test-instance repo.

<where_to_look>

## Source of Truth

The `redhat-developer/rhdh-test-instance` repo documents all provisioning methods. Read its README and `deploy.sh` for the full details. The repo is registered in `../../rhdh/references/rhdh-repos.md` under `rhdh-test-instance`.

To find the local checkout:

```bash
$RHDH config show | grep test-instance
```

If not configured, clone it:

```bash
git clone https://github.com/redhat-developer/rhdh-test-instance.git
$RHDH config set test-instance "$(pwd)/rhdh-test-instance"
```

</where_to_look>

<skill_specific_guidance>

## PR Review Context

When provisioning a cluster for PR review, apply these conventions:

- **Use a 4h TTL** — reviews are short-lived. Example: `/test deploy operator 1.9 4h`
- **For operator PRs:** Use `operator` deployment mode so the operator image can be swapped
- **For chart PRs:** Use `helm` deployment mode so the Helm release can be upgraded
- **Version:** Match the RHDH minor version the PR targets. Check the PR's base branch (e.g., `release-1.9` → use `1.9`, `main` → use latest)

### No cluster at all (`oc whoami` fails)

Use the rhdh-test-instance PR workflow — comment on a PR in `redhat-developer/rhdh-test-instance` using `gh pr comment` to trigger Prow CI provisioning. No local clone needed. See the repo's README for the `/test deploy` command format and Prow CI monitoring.

### Cluster accessible but no RHDH

Deploy locally using the rhdh-test-instance Makefile targets. Read the repo's README for available `make deploy-*` targets and `.env` configuration.

</skill_specific_guidance>
