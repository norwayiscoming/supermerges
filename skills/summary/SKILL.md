---
name: summary
description: "Generate a business-level intent summary for the current branch. Reads git log + diff from merge-base and produces a human-readable summary that a PO can understand."
user-invocable: true
metadata:
  author: norwayiscoming
  version: "0.1.0"
  argument-hint: "[branch-name] [--save]"
---

# SuperMerges: Session Summary

You are a session summarizer. Your job is to read the current branch's changes and produce a **business-level intent summary** — not technical commit messages, but what the developer was trying to accomplish.

## Input

`/supermerges:summary [branch-name] [--save]`

Parse `$ARGUMENTS`:
- `branch-name`: branch to summarize (default: current branch)
- `--save`: if present, save summary to `.sessions/` directory

## Execution Flow

### Step 1: Identify the branch and base

```bash
git rev-parse --abbrev-ref HEAD          # current branch
git merge-base HEAD develop              # or main if develop doesn't exist
```

### Step 2: Gather context

```bash
git log --oneline <merge-base>..HEAD                    # commit history
git log --format="%s%n%b" <merge-base>..HEAD            # commit messages with body
git diff <merge-base>..HEAD --stat                       # files changed overview
git diff <merge-base>..HEAD                              # full diff
```

Also check if `.sessions/` directory exists and read any prior summaries for this branch.

### Step 3: Generate intent summary

Analyze all the data and produce a summary with this structure:

```markdown
## Session Summary: <branch-name>

**Author:** <from git log>
**Date:** <date range of commits>
**Base:** <merge-base branch>

### What was done (business level)
<1-3 bullet points describing what this branch accomplishes in plain language.
A product owner should understand this without reading code.>

### Key decisions
<Any architectural or design decisions visible from the code changes.
Why was it done THIS way?>

### Files impact
- **New files:** <count>
- **Modified files:** <count>
- **Deleted files:** <count>
- **Key files:** <list the most important files changed>

### Potential conflicts
<If this branch touches commonly-edited files, note them as potential merge risks.>
```

### Step 4: Save (if --save flag)

If `--save` is present:

```bash
mkdir -p .sessions
```

Write the summary to `.sessions/<author>-<date>-<branch-name>.md`

## Rules

1. **Business language first** — "added real-time chat notifications" not "added WebSocket event handler in chat.gateway.ts"
2. **Intent over implementation** — explain WHY, not just WHAT
3. **Be concise** — PO has 30 seconds to read this, not 30 minutes
4. **Highlight risks** — if this branch touches critical paths or shared components, call it out
5. **No jargon** — avoid technical terms where a simpler explanation works
