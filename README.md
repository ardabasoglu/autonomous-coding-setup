# Autonomous Coding Setup

[![View on skills.sh](https://img.shields.io/badge/skills.sh-view%20skill-111111?logo=vercel&logoColor=white)](https://skills.sh/ardabasoglu/autonomous-coding-setup)

Prepare repositories for safer autonomous AI coding across multiple coding agents and harnesses. The skill detects the repository's existing setup, separates universal safety controls from agent-specific configuration, explains the gaps, and—only after approval—generates a reviewable setup.

## Supported harnesses

- Claude Code
- OpenAI Codex
- Cursor
- Generic or unknown coding agents

Support is based on the configuration that the target harness actually reads. When a harness format is unclear, the skill reports the limitation instead of guessing.

## Install with skills.sh

Install the repository:

```bash
npx skills add ardabasoglu/autonomous-coding-setup
```

Install only this skill:

```bash
npx skills add ardabasoglu/autonomous-coding-setup --skill autonomous-coding-setup
```

## Use it

Ask your coding agent:

> Prepare this repository for safer autonomous coding with Cursor and Codex.

Or specify a single harness:

> Prepare this repository for safer autonomous Claude Code sessions.

If no harness is named, the skill detects existing configuration and asks whether to configure the detected targets or only the generic controls.

## What it checks

### Universal controls

- Secret-file and credential protection
- Least-privilege and destructive-operation safeguards
- Verification commands for lint, tests, and builds
- CI gates for pull requests
- Non-zero failure handling
- Sandbox or isolated execution opportunities
- Human approval for irreversible actions

### Agent-specific configuration

- Claude Code: `CLAUDE.md`, `.claude/`
- Codex and generic agents: `AGENTS.md`
- Cursor: `.cursor/rules/`

## What it can generate

Depending on the repository and selected harness, it may propose or update:

```text
CLAUDE.md
AGENTS.md
.cursor/rules/
.github/workflows/agent-verification.yml
scripts/verify-agent-work.*
```

It preserves existing configuration and uses targeted edits rather than overwriting files. It does not assume that one vendor's permission syntax works for another.

## Safety model

The skill is local-first:

- It does not upload source code.
- It does not read or print secret values.
- It shows findings before making changes.
- It requires approval for proposed edits.
- It does not commit, push, open pull requests, provision infrastructure, or modify credentials automatically.
- It does not claim that any agent is safe or that a generated harness guarantees safe execution.

Review all generated changes before committing them.

## Scope and limitations

This is a repository preparation workflow, not a complete autonomous-agent security platform. It does not provision cloud environments, rotate credentials, configure every vendor's sandbox, or replace CI, security review, or human judgment. Agent-specific support depends on the target's documented behavior and the configuration present in the repository.

## License

MIT
