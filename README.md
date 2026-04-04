# Commit Prompt

An Agent Skill that follows disciplined Git workflows: break large code changes into small, modular, atomic commits; produce a Commit Plan; verify step by step; and draft a pull request description. Works with Cursor, Codex, OpenCode, Claude Code, and similar AI coding assistants.

## Features

- **Atomic commits**: Split big changes into single-purpose commits you can validate on their own
- **Commit Plan**: Plan before you code—clear intent and verification for each commit
- **Conventional Commits**: Consistent, spec-compliant commit messages
- **PR draft**: Auto-generated PR text with a verification checklist, risks, and rollback notes

## When to use

- Committing code or opening a PR
- Untangling a large set of changes
- Standardizing how you commit
- Any conversation that mentions commit, push, or pull request

## Quick start

```bash
# 1. Install (npx recommended)
npx add-skill qCanoe/commit-prompt-skill -g

# 2. Invoke in an agent chat
# Say: "Help me commit these changes" or "Create a PR"
# Or use /commit-prompt if your client supports it
```

## Installation

### Option A: npx (recommended)

Use [add-skill](https://add-skill.org/) for one-command setup across Cursor, Codex, OpenCode, Claude Code, and more:

```bash
# Project-local install
npx add-skill qCanoe/commit-prompt-skill

# User-wide install (available everywhere)
npx add-skill qCanoe/commit-prompt-skill -g

# Non-interactive (CI/CD)
npx add-skill qCanoe/commit-prompt-skill -g -y
```

### Option B: Manual install

Clone or copy this repo into your agent’s skills directory:

```bash
# Cursor
git clone https://github.com/qCanoe/commit-prompt-skill.git ~/.cursor/skills/commit-prompt

# Codex
git clone https://github.com/qCanoe/commit-prompt-skill.git ~/.codex/skills/commit-prompt

# OpenCode
git clone https://github.com/qCanoe/commit-prompt-skill.git ~/.config/opencode/skill/commit-prompt

# Claude Code
git clone https://github.com/qCanoe/commit-prompt-skill.git ~/.claude/skills/commit-prompt
```

## Core principles

### Atomic commit requirements

- **Single responsibility**: One logical change per commit
- **Buildable**: After every commit, the codebase should still run
- **Verifiable**: Attach evidence (short summary of tests / lint / build)
- **Readable**: Aim for ≤300 lines per commit diff (if larger, explain why)

### Avoid

- ❌ Mixing unrelated edits in one commit (formatting, renames, dependency bumps, features)
- ❌ One giant “everything” commit
- ❌ Restructuring the codebase while shipping new behavior in the same commit

## Workflow

### Phase 1: Prep

1. Create a branch: `feature/<topic>` or `fix/<topic>`
2. Sync the default branch: `git fetch --all --prune`, then `git rebase origin/main`
3. Baseline checks: run tests + build + lint; capture a short summary of the output

### Phase 2: Plan

**Before writing code, output a Commit Plan.** Planning rules:

- Infra / refactors before features
- Add or extend tests before changing behavior
- Whole-repo formatting gets its own commit—first or last

### Phase 3: Execute

For each slice, repeat:

1. Implement the change
2. Run the verification commands that belong to that slice
3. Stage precisely with `git add -p` (avoid blind `git add .`)
4. Commit using Conventional Commits
5. Emit a Commit Record
6. Push (batch pushes are fine—as long as each commit was verified)

### Phase 4: PR

Finish with a PR description draft (Summary, Scope, Verification, Risk & Rollback).

## Commit message format

Follow [Conventional Commits](https://www.conventionalcommits.org/):

```
type(scope): imperative summary

[optional body]

[optional footer]
```

**Types**: `feat` | `fix` | `refactor` | `chore` | `test` | `docs` | `build` | `ci` | `perf` | `style`

## Repository layout

```
commit-prompt/
├── SKILL.md      # Skill definition, workflow, checklist
├── templates.md  # Commit Plan, Commit Record, PR draft, scope hints
├── examples.md   # Six walkthroughs (auth, bugfix, formatting, deps, docs/CI, split vs merge)
├── README.md     # This file
└── LICENSE       # MIT
```

## Templates and examples

- Full templates: [templates.md](templates.md)
- Worked examples: [examples.md](examples.md)

## Troubleshooting

| Symptom | Likely cause | What to do |
|---------|----------------|------------|
| Skill not loading | Wrong install path | Confirm `~/.cursor/skills/commit-prompt` exists and contains `SKILL.md` |
| `add-skill` missing | No Node.js | Install Node.js 18+ or use manual `git clone` |
| Default branch isn’t `main` | Project uses `master`, etc. | Say so in chat, or replace `origin/main` in the skill |
| Different verify commands | Repo-specific tooling | Tell the agent your test / lint / build commands |

## License

MIT

## Contributing

Issues and pull requests are welcome.
