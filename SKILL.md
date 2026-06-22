---
name: prepare-branch-context
description: Use when starting work on a branch and needing full context before answering questions, reviewing code, or implementing changes. Analyzes the diff from the base branch, commit history, and linked PR (title, body, review comments) to build a complete picture of what the branch does and why.
license: MIT
compatibility: Requires git. The PR lookup step uses the GitHub CLI (gh); skip or adapt it for GitLab, Bitbucket, or other platforms. Read-only — makes no file changes.
allowed-tools: Bash(git:*) Bash(gh:*) Read
user-invocable: true
when_to_use: "Trigger phrases: 'prepare context', 'orient me on this branch', 'what does this PR do', 'load branch context', 'before we start'."
metadata:
  author: jnsahaj
  version: "1.0"
---

# Prepare Branch Context

Build a comprehensive understanding of the current branch so you can handle followup requests with full context.

## Steps

1. **Identify the current branch and the base branch**:
   ```bash
   git branch --show-current
   git merge-base HEAD main   # replace 'main' with 'master' or your default branch if needed
   ```

2. **Gather the diff from the base branch**:
   ```bash
   git diff main...HEAD --stat
   git diff main...HEAD
   ```
   Read through the full diff carefully. Understand what files were changed, added, or removed, and what the changes do.

3. **Review the commit history on this branch**:
   ```bash
   git log main..HEAD --oneline
   ```
   Read commit messages to understand the progression of changes.

4. **Check for a related PR or MR**:

   - **GitHub** (requires `gh`):
     ```bash
     gh pr list --head "$(git branch --show-current)" --json number,title,body,url,state,comments --jq '.[0]'
     ```
   - **GitLab / Bitbucket / other platforms**: use your platform's CLI or API equivalent to fetch the open MR/PR for the current branch.

   If a PR or MR exists, read its title, description, and comments for additional context (acceptance criteria, discussion, review feedback).

5. **Summarize your understanding** to the user:
   - What this branch does (high-level purpose)
   - Key files and areas of the codebase affected
   - Notable decisions or patterns visible in the changes
   - Any open review comments or discussion from the PR/MR
   - Current state (clean, uncommitted changes, etc.)

6. **Signal readiness**: Tell the user you're ready for followup questions or tasks related to this branch.

## Rules

- Do NOT make any changes to files. This skill is read-only.
- Read the actual diff content, not just the stat summary. You need to understand the code changes.
- If the diff is very large, focus on understanding the overall structure first, then read key files in detail.
- If the branch is the default branch or has no divergence from it, tell the user there is nothing to analyze.
