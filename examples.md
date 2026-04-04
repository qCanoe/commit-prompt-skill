# Commit Prompt examples

## Example 1: User authentication

### Commit Plan

**Branch**: `feature/user-auth`

**Baseline checks**:
- Command: `npm test && npm run lint && npm run build`
- Result: ✅ 45 tests passed, 0 lint errors, build successful

### Commits (ordered)

#### 1. `refactor(db): extract database connection module`
- **Intent**: Extract DB connection logic so adding a users table later is straightforward
- **Files**: `src/db/connection.ts`, `src/db/index.ts`
- **Verification**: `npm test -- --grep "database"`
- **Notes**: Refactor only—no behavior change

#### 2. `feat(db): add user table migration`
- **Intent**: Add the database migration for the users table
- **Files**: `migrations/001_create_users.sql`
- **Verification**: `npm run migrate:dry-run`
- **Notes**: May need DBA review

#### 3. `feat(auth): implement password hashing utility`
- **Intent**: Add password hashing helpers
- **Files**: `src/utils/crypto.ts`, `src/utils/crypto.test.ts`
- **Verification**: `npm test -- --grep "crypto"`
- **Notes**: bcrypt; tests first

#### 4. `feat(auth): add user registration endpoint`
- **Intent**: Implement the registration API
- **Files**: `src/routes/auth.ts`, `src/routes/auth.test.ts`
- **Verification**: `npm test -- --grep "auth"`
- **Notes**: Depends on prior commits

---

### Commit Records

#### Commit 1

**Commit**: `a1b2c3d` `refactor(db): extract database connection module`

**Why**: Connection logic was scattered; a single module simplifies pooling and future user-table work. Pure groundwork before features.

**Verification**:
- Command: `npm test -- --grep "database"`
- Result: ✅ 12 tests passed

**Push**: Not yet—will push once the feature slice is complete

---

#### Commit 2

**Commit**: `d4e5f6g` `feat(db): add user table migration`

**Why**: Auth needs persisted users; this is the data-layer step and stays separate from the refactor commit.

**Verification**:
- Command: `npm run migrate:dry-run`
- Result: ✅ Migration validated; would create `users` with 5 columns

**Push**: Not yet

---

### PR description

## Summary
Add user registration with email sign-up and secure password storage.

## Scope

| Commit | Type | Scope | Description |
|--------|------|-------|-------------|
| a1b2c3d | refactor | db | Extract database connection module |
| d4e5f6g | feat | db | Add user table migration |
| h7i8j9k | feat | auth | Implement password hashing utility |
| l0m1n2o | feat | auth | Add user registration endpoint |

## Verification
- [x] Unit tests: `npm test` ✅ (57 tests, +12 new)
- [x] Lint: `npm run lint` ✅
- [x] Build: `npm run build` ✅
- [x] Manual testing: Registration flow exercised via Postman

## Risk & Rollback

### Risks
- Migrations are hard to undo (dropping `users` loses data)
- bcrypt cost factor can affect latency under load

### Rollback Strategy
- Revert code: `git revert l0m1n2o h7i8j9k d4e5f6g a1b2c3d`
- Data: if migration ran, manually `DROP TABLE users` per policy
- Note: reverting drops data for users created after deploy

---

## Example 2: Bugfix

### Commit Plan

**Branch**: `fix/date-timezone`

**Baseline checks**:
- Command: `npm test`
- Result: ⚠️ 2 tests failing (expected—this is the bug under repair)

### Commits (ordered)

#### 1. `test(reports): add failing test for timezone issue`
- **Intent**: Capture the regression with a failing test first
- **Files**: `src/reports/date.test.ts`
- **Verification**: `npm test -- --grep "timezone"` (expect failure)
- **Notes**: TDD-style fix

#### 2. `fix(reports): correct date formatting in timezone conversion`
- **Intent**: Fix the timezone conversion logic
- **Files**: `src/reports/date.ts`
- **Verification**: `npm test -- --grep "timezone"` (expect pass)
- **Notes**: Closes #456

---

## Example 3: Formatting mixed with refactors

### ❌ Bad

```
feat(parser): add JSON support and fix formatting

- Added JSON parsing capability
- Fixed indentation across 50 files
- Renamed parseData to parseInput
- Updated dependencies
```

One commit, four unrelated change types—hard to review or revert cleanly.

### ✅ Good

Split into four commits:

1. `style: fix indentation across codebase`
2. `refactor(parser): rename parseData to parseInput`
3. `chore(deps): update lodash to v4.17.21`
4. `feat(parser): add JSON parsing support`

Each can be validated, reviewed, and reverted on its own.

---

## Example 4: Dependency upgrade

### Commit Plan

**Branch**: `chore/deps-axios`

**Baseline checks**:
- Command: `npm test && npm run build`
- Result: ✅ all green

### Commits (ordered)

#### 1. `chore(deps): upgrade axios from 0.27 to 1.6`
- **Intent**: Bump axios and adapt to API changes
- **Files**: `package.json`, `package-lock.json`, `src/api/client.ts`
- **Verification**: `npm test -- --grep "api"`
- **Notes**: Read breaking changes; update call sites

#### 2. `docs(deps): add axios migration note to CHANGELOG`
- **Intent**: Document the upgrade for the team
- **Files**: `CHANGELOG.md`
- **Verification**: n/a
- **Notes**: Docs only

---

## Example 5: Docs and CI

### Commit Plan

**Branch**: `docs/readme-and-ci`

**Baseline checks**:
- Command: `npm test`
- Result: ✅ passed

### Commits (ordered)

#### 1. `docs: add API usage examples to README`
- **Intent**: Expand README with API examples
- **Files**: `README.md`
- **Verification**: n/a
- **Notes**: No code changes

#### 2. `ci: add GitHub Actions workflow for lint`
- **Intent**: Lint on every PR automatically
- **Files**: `.github/workflows/lint.yml`
- **Verification**: `npm run lint` locally
- **Notes**: Split from README so either change can be reverted alone

---

## Example 6: When one commit is enough vs. many

### Small change: single commit

```bash
# One file, tiny bugfix
fix(auth): correct token expiry validation
```

### Large or mixed change: must split

| Change pattern | Split? | Example |
|----------------|--------|---------|
| Formatting + feature | ✅ Yes | Separate `style:` and `feat:` |
| Rename + behavior | ✅ Yes | Separate `refactor:` and `feat:` |
| Multiple modules | ✅ Yes | Multiple commits by scope |
| Same bug, several call sites | ❌ Often one | Single `fix(scope):` |
| Docs only | ❌ Often one | Single `docs:` |
