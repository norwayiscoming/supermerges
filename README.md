# SuperMerges

AI-powered intent-aware merge tool for vibecoding teams. A Claude Code plugin.

## Problem

When multiple developers vibecode on the same feature, AI generates thousands of lines in minutes. Traditional git merge only understands text diff — it loses the **intent** behind each developer's work.

SuperMerges merges at the **intent level**, not just the code level.

## Install

```bash
claude plugins add github:norwayiscoming/supermerges
```

Or test locally:

```bash
claude --plugin-dir ./supermerges
```

## Commands

### `/supermerges:merge <branch-a> <branch-b>`

Smart merge two branches:
- Parallel analysis using git worktrees
- Business-level intent summaries for both branches
- AI-powered conflict resolution that understands WHY changes were made
- PO approval before any merge happens

### `/supermerges:summary [branch] [--save]`

Generate intent summary for a branch:
- Reads git log + diff from merge-base
- Produces PO-readable summary (not technical commit messages)
- `--save` stores to `.sessions/` for future merge context

### `/supermerges:status [--remote]`

Team overview dashboard:
- All active feature branches with author, progress, last activity
- Cross-branch conflict detection
- Merge recommendations

### `/supermerges:review <branch> [--into <target>]`

PO review mode:
- Non-technical change summary
- Risk assessment (low/medium/high)
- Approve / Request changes / Reject flow
- Merges locally on approve (never auto-pushes)

## How It Works

```
Developer codes → AI session tracked → Intent summary generated
                                              ↓
PO runs /supermerges:status          → sees all branches + conflicts
PO runs /supermerges:merge A B       → AI merges at intent level
PO runs /supermerges:review branch   → approves → local merge done
```

Key principles:
- **Git worktree isolation** — all analysis happens in temporary worktrees, never touches your workspace
- **Intent over text** — understands WHY changes were made, not just WHAT changed
- **PO-friendly** — summaries in business language, not technical jargon
- **Safe by default** — never pushes, never modifies without approval

## Session Tracking

The plugin includes hooks that auto-track session metadata when a Claude Code session ends on a feature branch. Data is saved to `.sessions/` and used to enrich merge analysis.

## License

MIT
