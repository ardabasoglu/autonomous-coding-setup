---
name: autonomous-coding-setup
description: Prepare repositories for safer autonomous coding.
version: 0.1.0
author: Arda Basoglu, Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [Autonomous Coding, AI Agents, Repository Safety, CI]
    related_skills: []
---

# Autonomous Coding Setup Skill

Use this skill to audit a repository before allowing long-running autonomous coding sessions, then generate a small, reviewable harness for safety and verification. It is designed to work locally and never needs secret values. It does not replace human review or claim that an agent is safe by default.

## When to Use

- The user asks to prepare a repository for safer autonomous AI coding.
- The user wants to harden Claude Code, Codex, Cursor, or another coding agent.
- The user wants permission rules, verification hooks, or CI gates generated.
- The user names one or more coding harnesses that should be configured.

Don't use for generic code review, cloud provisioning, credential rotation, or deploying production infrastructure.

## Harness detection and adapters

Treat safety and verification as universal controls, then translate them into the requested or detected harness format. Detect existing configuration before generating files:

| Harness | Project files to inspect |
|---|---|
| Claude Code | `CLAUDE.md`, `.claude/` |
| Codex | `AGENTS.md`, project instructions |
| Cursor | `.cursor/rules/`, project rules |
| Generic or unknown agent | `AGENTS.md`, scripts, CI, sandbox and policy files |

If the user names no harness, detect present configuration and ask whether to configure all detected targets or only the generic controls. Never copy one vendor's syntax into another vendor's file. If a target's current format is undocumented or unclear, generate the vendor-neutral controls and report the adapter limitation.

Universal controls include secret isolation, verification commands, CI gates, non-zero failure handling, least privilege, sandboxing, and approval for irreversible actions. Harness adapters should only express those controls in files that the target agent actually reads.
## Prerequisites

- Work from the repository root.
- Inspect the repository before editing it.
- Use `read_file`, `search_files`, and `terminal` for discovery and verification.
- Never read or print secret values. Treat `.env*`, private keys, tokens, and credential files as sensitive.

## Procedure

### 1. Establish scope and inspect safely

Confirm the repository root with `terminal(command="pwd")` and inspect only names first with `search_files(target="files")`. Look for:

- Harness instruction files and configuration: `CLAUDE.md`, `AGENTS.md`, `.claude/`, `.cursor/`, and other agent-specific files
- `package.json`, `pyproject.toml`, `Makefile`, or equivalent project commands
- `.github/workflows/` or another CI directory
- `.env*`, credential directories, private-key paths, and deployment scripts

Read configuration and instruction files with `read_file`. Do not cat or print the contents of secret files.

Completion criterion: every proposed change maps to an existing repository file or an explicitly missing control.

### 2. Detect the four core gaps

Report findings before editing. Check:

1. **Secret protection** — Are `.env*`, SSH keys, cloud credentials, and destructive Git operations protected by explicit deny rules or equivalent controls?
2. **Verification hook** — Is there a deterministic hook or script that runs the relevant lint, test, and build checks before completion?
3. **CI gate** — Does CI run the same checks for every pull request and fail when checks fail?
4. **Instruction quality** — Does `CLAUDE.md` contain durable project constraints, or mostly obvious, stale, repetitive, or contradictory instructions?

Classify findings as critical, high, or medium. Explain the risk and name the proposed file change.

Completion criterion: present the findings and wait for approval before writing files.

### 3. Propose files conservatively

After approval, propose only the fixes selected by the user. Preserve existing rules and merge rather than overwrite. Use `write_file` only for new files; use `patch` for targeted edits to existing files.

For agent-specific protection, generate or extend the configuration for each selected harness. Keep universal protection in repository policy, sandbox, and CI controls. Rules equivalent to the following may be appropriate, but syntax and scope must be adapted to the target agent:

```text
Read(.env*)
Read(~/.ssh/**)
Read(~/.aws/**)
Bash(git push --force*)
```

Adapt syntax to the agent and repository. Do not assume one vendor's configuration format is valid for another.

For verification, create a project-specific script that uses the repository's existing commands. A failing check must return a non-zero exit code; do not hide failures with `|| true`.

For CI, generate a minimal pull-request workflow that installs dependencies using the lockfile and runs the existing test/build checks. Do not invent commands when the project already documents them.

For instruction files, retain only durable constraints, legacy context, architectural decisions, and commands that are not discoverable from the repository. Apply this to `CLAUDE.md`, `AGENTS.md`, Cursor rules, or another detected target. Do not delete project guidance merely because it is short.

Completion criterion: show a file-by-file change preview and ask for approval before applying it if the user has not already approved the exact files.

### 4. Apply and verify

Apply approved edits, then inspect every modified file with `read_file`. Run the project's relevant checks with `terminal`. At minimum:

- Parse generated JSON/YAML where practical.
- Run lint, tests, and build commands that exist in the project.
- Confirm sensitive values were not added to generated files.
- Confirm every generated script exits non-zero when a required check fails.
- Confirm `git diff --check` is clean.

Completion criterion: all available checks pass, or failures are reported with the exact command and output; never claim verification if a check was unavailable.

### 5. Finish with a reviewable handoff

Report:

- Findings fixed and findings intentionally left unresolved
- Exact files changed
- Commands run and their results
- Any assumptions about the project's commands
- A reminder that generated configuration must be reviewed before commit

Do not commit, push, open a pull request, provision infrastructure, or modify secrets unless the user separately requests that action.

## Generated-file guidance

Prefer these files when they fit the repository and selected harness:

```text
CLAUDE.md / .claude/
AGENTS.md
.cursor/rules/
.github/workflows/agent-verification.yml
scripts/verify-agent-work.*
```

Use the repository's existing conventions when paths, CI providers, or agent configuration differ.

## Pitfalls

- A deny rule protects a named tool call but may not constrain side effects of scripts it launches; use sandboxing or isolated environments for untrusted execution.
- CI is a final gate, not a replacement for least privilege, secret isolation, or human review.
- Do not expose secret contents while scanning. File names and presence are enough for this audit.
- Do not overwrite an existing `CLAUDE.md`, settings file, hook, or workflow without a targeted merge.
- Exit code `0` from a wrapper that swallowed a failing test makes the harness appear healthy; preserve failures.
- Generated configuration is vendor- and project-specific. Validate syntax and behavior rather than assuming the example is universal.
- Do not claim support for a harness merely because it can read a generic Markdown file; identify the target's actual instruction, permission, and sandbox mechanisms.

## Verification checklist

- [ ] Harnesses were detected or explicitly selected.
- [ ] Universal controls were separated from harness-specific adapters.
- [ ] Unsupported or unclear harness behavior was reported.
- [ ] Repository was inspected before edits.
- [ ] Secret values were not read or printed.
- [ ] Findings were shown before changes.
- [ ] User-approved files only were changed.
- [ ] Existing protections were preserved.
- [ ] Tests, lint, and build checks were run where available.
- [ ] `git diff --check` passes.
- [ ] No automatic commit or push occurred without a separate request.
