---
name: status
description: "Overview of all feature branches: who is working on what, how far along, and where potential conflicts exist between branches."
user-invocable: true
metadata:
  author: norwayiscoming
  version: "0.1.0"
  argument-hint: "[--remote]"
---

# SuperMerges: Team Status

You are a team status reporter. Your job is to give the PO (or any team member) a quick overview of all active feature branches and potential conflicts.

## Input

`/supermerges:status [--remote]`

- `--remote`: include remote-only branches (default: local + remote tracking)

## Execution Flow

### Step 1: List active branches

```bash
git branch -a --sort=-committerdate --format='%(refname:short) %(committerdate:relative) %(authorname)'
```

Filter out `main`, `develop`, `HEAD` — focus on feature branches.

### Step 2: For each active branch

```bash
git log --oneline develop..<branch> | wc -l              # commits ahead
git diff develop..<branch> --stat --stat-count=5          # top files changed
git diff develop..<branch> --shortstat                    # summary +/-
```

### Step 3: Detect cross-branch conflicts

For every pair of active branches, check if they touch the same files:

```bash
# Files changed by branch A (from develop)
git diff develop..<branch-a> --name-only

# Files changed by branch B (from develop)  
git diff develop..<branch-b> --name-only

# Intersection = potential conflict
comm -12 <(sort filelistA) <(sort filelistB)
```

### Step 4: Read session summaries

If `.sessions/` directory exists, read summaries to enrich the report with intent context.

### Step 5: Output report

```markdown
## 📊 SuperMerges Team Status

### Active Branches

| Branch | Author | Commits | Files | Last Activity |
|--------|--------|---------|-------|---------------|
| feat/chat | Hiru | 4 | 15 (+800/-200) | 3 min ago |
| feat/feed | Slug | 3 | 8 (+500/-50) | 10 min ago |
| feat/notify | Me | 2 | 5 (+300/-20) | 1 hour ago |

### Intent Summaries (from .sessions/)
- **feat/chat:** "Tách chat thành 3 sub-components..."
- **feat/feed:** "Thêm infinite scroll..."

### ⚠️ Potential Conflicts

| Branch A | Branch B | Overlapping Files |
|----------|----------|-------------------|
| feat/chat | feat/feed | ChatComponent.tsx, types.ts |

### Recommendations
- feat/chat ↔ feat/feed: merge sớm để tránh diverge thêm
- feat/notify: safe to merge independently
```

## Rules

1. **Sort by risk** — branches with conflicts first
2. **Actionable** — always include a recommendation
3. **Quick scan** — PO should understand the situation in 10 seconds
4. **Session context** — use `.sessions/` summaries when available
