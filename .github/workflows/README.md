# GitHub Actions Workflows

## Workflows

### 🔒 DCO Check (`dco.yml`)

Verifies all commits in pull requests have proper DCO sign-off.

**Triggers:**

- Pull request opened, synchronized, or reopened

**What it does:**

1. Fetches all commits from the PR
2. Checks each commit for `Signed-off-by` line
3. Fails if any commit is missing DCO sign-off

**Fix missing sign-off:**

```bash
# Amend last commit
git commit --amend --signoff --no-edit
git push --force

# Sign multiple commits
git rebase HEAD~3 --signoff
git push --force
```

---

## Quick Reference

| Workflow  | Trigger       | Purpose                  |
| --------- | ------------- | ------------------------ |
| DCO Check | Pull requests | Enforce commit sign-off  |
