# prepare-branch-context

> An agent skill that analyzes a branch's diff, commit history, and linked PR to build full context before answering questions or implementing changes.

Before diving into review comments, bug reports, or follow-up tasks on a branch, an agent needs to understand what the branch *is*. This skill does that groundwork: it reads the full diff from the base branch, walks the commit log, fetches the PR title/description/comments if one exists, and surfaces a plain-language summary — then tells you it's ready for whatever comes next.

## Inspiration

This skill is directly inspired by [jnsahaj's prepare-branch-context gist](https://gist.github.com/jnsahaj/fc47181ecf7c5c248a88f18a369c0a63). The original captures the exact right workflow, so the steps and rules are preserved faithfully.

A proper repository was created around it for a few practical reasons:

- **Installable via standard tooling** — `gh skill install` and `npx skills add` work against GitHub repos, not gists
- **Versioned and updatable** — consumers can pin to a release and run `gh skill update --all` to stay current
- **Agent-provider-agnostic** — the spec frontmatter (`allowed-tools`, `compatibility`) makes the skill portable across Claude Code, Cursor, Codex, Copilot, and other Agent-Skills-compatible runtimes
- **Claude Code enhancements** — Claude Code-specific fields (`model`, `when_to_use`, `user-invocable`) improve the experience on Claude without affecting other runtimes, which simply ignore unknown frontmatter
- **Discoverable** — shows up on [skills.sh](https://www.skills.sh) and in agent skill registries; a gist does not

## What it does

1. Identifies the current branch and its merge-base with the default branch
2. Reads the full diff (not just `--stat`) so the agent understands the actual code changes
3. Walks commit messages to understand the progression of work
4. Fetches the linked PR/MR — title, description, and review comments — for acceptance criteria and discussion context
5. Delivers a plain-language summary: purpose, affected areas, notable decisions, open review threads, and current working-tree state
6. Signals readiness for follow-up questions or tasks

## Claude Code enhancements

When used in Claude Code, three extra frontmatter fields activate automatically and are ignored by all other runtimes:

| Field | Value | Effect |
| :---- | :---- | :----- |
| `model` | `claude-haiku-4-5-20251001` | Uses a fast model — this skill is read-only analysis, not complex reasoning |
| `user-invocable` | `true` | Exposes `/prepare-branch-context` as a slash command |
| `when_to_use` | trigger phrases | Helps Claude auto-detect when to invoke the skill from natural-language prompts |

## Install

### GitHub CLI (`gh skill`)

Requires [`gh`](https://cli.github.com/) v2.90.0+.

```bash
# Install interactively (auto-detects configured agents)
gh skill install xpepper/prepare-branch-context

# Install and target a specific agent explicitly
gh skill install xpepper/prepare-branch-context --agent claude-code

# Install to user scope (global, not project-local)
gh skill install xpepper/prepare-branch-context --scope user

# Stay up to date
gh skill update --all
```

Skills are installed to `.agents/skills/` at project scope by default — shared automatically across Claude Code, GitHub Copilot, Cursor, Codex, OpenCode, and other Agent-Skills-compatible runtimes. Use `--scope user` to install globally.

### npx (`skills` CLI)

Works on any machine with Node.js. No GitHub CLI required.

```bash
npx skills add xpepper/prepare-branch-context
```

Launches an interactive picker to choose your target agent (Claude Code, Cursor, Codex, …). To skip the picker and install non-interactively:

```bash
npx skills add xpepper/prepare-branch-context --yes
```

## Usage

Once installed, invoke it directly:

```
/prepare-branch-context
```

Or trigger it naturally — in Claude Code, phrases like "orient me on this branch", "what does this PR do", or "load branch context" will auto-invoke it.

## Requirements

- `git` — for diff, log, and branch commands
- `gh` (optional) — for the PR lookup step on GitHub. Omit or replace with your platform's equivalent for GitLab, Bitbucket, or Azure DevOps.

## License

MIT
