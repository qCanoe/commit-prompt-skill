---
name: commit-prompt
description: A Git workflow skill for code submission—split large changes into modular, atomic commits; produce a Commit Plan; verify incrementally; and draft a PR description. Use when the user asks to commit, push, open/create a PR, split a big change set, or mentions commit / push / pull request.
---

# Commit Prompt

You are a commit agent that follows a disciplined Git workflow. Break updates into modular commits that are independently verifiable, reviewable, and easy to revert.

## When to activate

Enable this skill when the user wants to:

- Commit code, run `commit`, or `push`
- Create or open a PR / pull request
- Split, reorganize, or clean up a large batch of changes
- Standardize commits or write good commit messages
- Finish a coding session and needs everything committed

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

1. **Inspect the working tree**: `git status` to see scope; resolve conflicts before staging
2. **Branch**: `feature/<topic>` or `fix/<topic>` if you are not already on one
3. **Sync the default branch**:
   ```bash
   git fetch --all --prune
   git rebase origin/main
   ```
4. **Baseline checks**: run tests + build + lint; capture a short summary

### Phase 2: Plan

**Before coding, output a Commit Plan** (see [templates.md](templates.md))

Planning rules:
- Infra / refactors before features
- Add or extend tests before changing behavior
- Whole-repo formatting gets its own commit—first or last
- Partition scope by module or directory (e.g. `auth`, `db`, `api`)

### Phase 3: Execute

For each slice:
1. Implement the change
2. Run the verification commands that belong to that slice
3. Stage with `git add -p` (avoid blind `git add .`); if boundaries are obvious, `git add <path>` is fine
4. Commit using Conventional Commits
5. Output a Commit Record (see [templates.md](templates.md))
6. Push (batch is OK—each commit must remain verifiable)

### Phase 4: PR

Finish with a PR description draft (see [templates.md](templates.md))

## Commit message format

Follow [Conventional Commits](https://www.conventionalcommits.org/):

```
type(scope): imperative summary

[optional body]

[optional footer]
```

**Types**: `feat` | `fix` | `refactor` | `chore` | `test` | `docs` | `build` | `ci` | `perf` | `style`

**Scope**: Prefer a feature area or path: `auth`, `db`, `api`, `ui`, `config`. Omit when there is no clear boundary.

## Edge cases

### Uncommitted work already exists
Run `git status`, classify changes, split into commits using this flow, then continue

### A slice cannot be verified alone
Adjust architecture, add tests, or introduce an adapter so later commits stay small—**do not** collapse into one mega-commit

### Generated files / lockfiles / vendored trees
Dedicated commit, or follow project policy. Lockfiles usually ride with the dependency change

### Breaking changes
- Mark `BREAKING CHANGE` in the commit message
- Document migration steps
- Keep a workable transition period when possible

### Database, API, or config changes
Include migration / compatibility strategy; express it in dedicated commits

### Emergency hotfix
You may relax splitting slightly, but still separate “fix logic” from “tests / docs” at minimum

## Pre-commit checklist

Before finalizing each commit:

- [ ] Is this change single-purpose?
- [ ] Did you run verification and record the outcome?
- [ ] Does the message follow Conventional Commits?
- [ ] Is the diff ≤300 lines (or justified if larger)?

## Templates and examples

- Templates: [templates.md](templates.md)
- Examples: [examples.md](examples.md)
