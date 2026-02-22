# Piranha PR Submission Summary

## PRs Submitted

| PR | Title | Status | URL |
|----|-------|--------|-----|
| #69 | Fix compilation warnings and C++ correctness issues | Open | https://github.com/ange-yaghi/piranha/pull/69 |
| #70 | Add cross-platform build support | Open | https://github.com/ange-yaghi/piranha/pull/70 |

Both have build verification comments added.

---

## Gap Analysis: Can We Use Upstream Master?

**NO** - If PRs #69 and #70 are accepted, there is still a gap:

| Feature | In PRs? | Required for CLI? |
|---------|---------|-------------------|
| Bug fixes | ✅ PR #69 | No (correctness only) |
| Platform support | ✅ PR #70 | Yes |
| **Mutation feature** | ❌ NOT SUBMITTED | **YES - Required** |

The mutation feature (2 commits) is needed because:
- Engine-sim uses `META_MUTATION` flag on nodes like `AddSampleNode`
- Without it, evaluation order is undefined
- CLI will not work correctly without it

**Conclusion**: We must maintain our fork regardless of PR acceptance, unless we:
1. Submit mutation feature as PR #3 (risky - may be rejected)
2. Refactor engine-sim to not need it (major effort)

---

## Branch Inventory

### Local Branches
| Branch | Purpose | Action |
|--------|---------|--------|
| `master` | Working solution (all changes) | KEEP - main branch |
| `pr/bugfixes` | PR #69 | KEEP until PR resolved |
| `pr/platform-support` | PR #70 | KEEP until PR resolved |
| `test/pr-combined` | Testing (all PRs + mutation) | KEEP or DELETE |
| `piranha-eval-fix` | Redundant | DELETE |

### Remote Branches (origin)
| Branch | Action |
|--------|--------|
| `origin/master` | KEEP |
| `origin/pr/bugfixes` | KEEP until PR resolved |
| `origin/pr/platform-support` | KEEP until PR resolved |
| `origin/test/pr-combined` | KEEP or DELETE |
| `origin/fix/macos-filesystem-api-fixes` | DELETE (merged) |
| `origin/feature/I53_free_memory` | KEEP (from upstream) |

---

## Other Active Forks

Only **one other active fork** (pushed in 2026):

| Fork | Last Updated | Stars | Notes |
|------|-------------|-------|-------|
| jak6jak/piranha | 2026-01-19 | 0 | Unknown changes |
| danieljsinclair/piranha | 2026-02-22 | 0 | **Yours** |

No better maintained alternative exists.

---

## Recommended Actions

### 1. Clean Up Redundant Branches

```bash
# Local
git branch -d piranha-eval-fix

# Remote (after PRs resolved)
git push origin --delete fix/macos-filesystem-api-fixes
git push origin --delete test/pr-combined  # optional
```

### 2. Decision on Mutation Feature

**Option A**: Keep in fork only (recommended)
- Accept we maintain a fork
- Simplest approach

**Option B**: Submit as PR #3
- Risky - may be rejected
- Would need strong justification

**Option C**: Refactor engine-sim
- 2-4 weeks effort
- Breaking change for .mr scripts

### 3. Prepare Fork for Long-term Maintenance

If PRs are rejected:
1. Master branch = our working solution
2. Ensure CLI builds against master
3. Document differences from upstream

---

## Next Steps

1. Wait for PR feedback (may take time - author inactive)
2. Clean up redundant branches
3. Decide on mutation feature approach
4. Ensure master is the default branch on GitHub
