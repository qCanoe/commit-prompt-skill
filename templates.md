# Commit Prompt templates

## Branch naming

| Kind | Pattern | Example |
|------|---------|---------|
| Feature | `feature/<topic>` | `feature/user-auth` |
| Bugfix | `fix/<topic>` | `fix/date-timezone` |
| Docs | `docs/<topic>` | `docs/api-readme` |
| Refactor | `refactor/<topic>` | `refactor/db-layer` |
| Dependencies | `chore/deps-<pkg>` | `chore/deps-lodash` |

---

## Commit Plan template

Emit this plan before you write code:

```markdown
## Commit Plan

**Branch**: `feature/<topic>` or `fix/<topic>`

**Baseline checks**:
- Command: `<verification command>`
- Result: `<short result summary>`

### Commits (ordered)

#### 1. `<type(scope): summary>`
- **Intent**: <one sentence: the sole purpose of this commit>
- **Files**: <expected paths or directories>
- **Verification**: <command you will run>
- **Notes**: <compat, risk, refactor-only, etc.>

#### 2. `<type(scope): summary>`
- **Intent**: ...
- **Files**: ...
- **Verification**: ...
- **Notes**: ...

(continue until every planned change is covered)
```

---

## Commit Record template

After each commit, output:

```markdown
## Commit Record

**Commit**: `<short-hash>` `<message>`

**Why**: <why this commit exists and how it differs from neighbors before/after>

**Verification**:
- Command: `<command>`
- Result: `<key outcome>`

**Push**: <pushed or not; if not, when and why>
```

---

## PR description template

Final PR draft:

```markdown
## Summary
<what changed; user-visible impact>

## Scope
<modules touched; maps to commit set>

| Commit | Type | Scope | Description |
|--------|------|-------|-------------|
| abc123 | feat | auth  | Add JWT validation |
| def456 | test | auth  | Add auth unit tests |

## Verification
<what you ran>

- [ ] Unit tests: `npm test` ✅
- [ ] Lint: `npm run lint` ✅
- [ ] Build: `npm run build` ✅
- [ ] Manual testing: <notes>

## Risk & Rollback

### Risks
- <risk 1>
- <risk 2>

### Rollback Strategy
- Revert commits: `git revert <hash1> <hash2>`
- Migrations: <yes/no and how to handle>
- Downstream services: <impact if any>

## Screenshots / Logs
<attach if useful>
```

---

## Commit message templates

### Standard

```
<type>(<scope>): <imperative summary>

<optional body — explain why, not what>

<optional footer>
```

### With breaking change

```
feat(api): change authentication endpoint

BREAKING CHANGE: /auth/login now requires email instead of username

Migration:
- Update client calls to use email field
- Run migration script: `npm run migrate:auth`
```

### With issue reference

```
fix(parser): handle empty input gracefully

Closes #123
```

---

## Verification commands by stack

Pick commands that fit the project:

### Node.js / TypeScript
```bash
npm test                    # unit tests
npm run lint               # lint
npm run build              # build
npm run typecheck          # types
```

### Python
```bash
pytest                     # unit tests
ruff check .              # lint
mypy .                    # types
python -m build           # build
```

### Rust
```bash
cargo test                 # unit tests
cargo clippy              # lint
cargo build --release     # build
```

### Go
```bash
go test ./...             # unit tests
golangci-lint run         # lint
go build ./...            # build
```

### Java / Kotlin
```bash
./gradlew test            # or mvn test
./gradlew check           # checks
./gradlew build           # build
```

### C# / .NET
```bash
dotnet test               # unit tests
dotnet format --verify    # format check
dotnet build              # build
```

### Ruby
```bash
bundle exec rspec         # unit tests
bundle exec rubocop       # lint
```

---

## Scope reference

Common scopes (module, directory, or capability):

| Scope | Meaning | Examples |
|-------|---------|----------|
| `auth` | Authentication / authorization | Login, JWT, permissions |
| `api` | API layer | Routes, controllers |
| `db` | Data layer | Migrations, models, ORM |
| `ui` | UI | Components, styling |
| `config` | Configuration | Env vars, config files |
| `deps` | Dependencies | package.json, requirements.txt |
| `ci` | Continuous integration | GitHub Actions, Jenkinsfile |
| `docs` | Documentation | README, API docs |
