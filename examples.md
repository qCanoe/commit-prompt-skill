# Commit Prompt 示例

## 示例 1: 添加用户认证功能

### Commit Plan

**Branch**: `feature/user-auth`

**Baseline checks**:
- Command: `npm test && npm run lint && npm run build`
- Result: ✅ 45 tests passed, 0 lint errors, build successful

### Commits (ordered)

#### 1. `refactor(db): extract database connection module`
- **Intent**: 将数据库连接逻辑抽取为独立模块，为后续添加用户表做准备
- **Files**: `src/db/connection.ts`, `src/db/index.ts`
- **Verification**: `npm test -- --grep "database"`
- **Notes**: 纯重构，无功能变更

#### 2. `feat(db): add user table migration`
- **Intent**: 添加用户表的数据库迁移脚本
- **Files**: `migrations/001_create_users.sql`
- **Verification**: `npm run migrate:dry-run`
- **Notes**: 需要数据库管理员审核

#### 3. `feat(auth): implement password hashing utility`
- **Intent**: 添加密码哈希工具函数
- **Files**: `src/utils/crypto.ts`, `src/utils/crypto.test.ts`
- **Verification**: `npm test -- --grep "crypto"`
- **Notes**: 使用 bcrypt，先写测试

#### 4. `feat(auth): add user registration endpoint`
- **Intent**: 实现用户注册 API
- **Files**: `src/routes/auth.ts`, `src/routes/auth.test.ts`
- **Verification**: `npm test -- --grep "auth"`
- **Notes**: 依赖前两个提交

---

### Commit Records

#### Commit 1

**Commit**: `a1b2c3d` `refactor(db): extract database connection module`

**Why**: 当前数据库连接逻辑散落在多处，抽取为独立模块便于后续添加用户表时复用连接池。这是功能开发前的基础设施准备。

**Verification**:
- Command: `npm test -- --grep "database"`
- Result: ✅ 12 tests passed

**Push**: 暂不 push，等功能完整后一起推送

---

#### Commit 2

**Commit**: `d4e5f6g` `feat(db): add user table migration`

**Why**: 用户认证需要存储用户信息，这是数据层的准备。与前一个重构分开，因为这是新增功能。

**Verification**:
- Command: `npm run migrate:dry-run`
- Result: ✅ Migration validated, would create table `users` with 5 columns

**Push**: 暂不 push

---

### PR 描述

## Summary
添加用户注册功能，支持邮箱注册和密码安全存储。

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
- [x] Manual testing: 通过 Postman 测试注册流程

## Risk & Rollback

### Risks
- 数据库迁移不可逆（DROP 用户表会丢失数据）
- bcrypt 配置的 cost factor 可能影响性能

### Rollback Strategy
- 回滚代码: `git revert l0m1n2o h7i8j9k d4e5f6g a1b2c3d`
- 数据迁移: 如已运行，需手动 `DROP TABLE users`
- 注意: 回滚后新注册用户数据将丢失

---

## 示例 2: Bug 修复

### Commit Plan

**Branch**: `fix/date-timezone`

**Baseline checks**:
- Command: `npm test`
- Result: ⚠️ 2 tests failing (预期，这是要修复的 bug)

### Commits (ordered)

#### 1. `test(reports): add failing test for timezone issue`
- **Intent**: 先添加失败测试来明确 bug 的重现条件
- **Files**: `src/reports/date.test.ts`
- **Verification**: `npm test -- --grep "timezone"` (预期失败)
- **Notes**: TDD 方式修复

#### 2. `fix(reports): correct date formatting in timezone conversion`
- **Intent**: 修复时区转换逻辑
- **Files**: `src/reports/date.ts`
- **Verification**: `npm test -- --grep "timezone"` (预期通过)
- **Notes**: Closes #456

---

## 示例 3: 格式化与重构混合

### ❌ 错误做法

```
feat(parser): add JSON support and fix formatting

- Added JSON parsing capability
- Fixed indentation across 50 files
- Renamed parseData to parseInput
- Updated dependencies
```

这个 commit 混合了 4 种不同类型的改动，无法单独回滚。

### ✅ 正确做法

拆分为 4 个独立提交：

1. `style: fix indentation across codebase`
2. `refactor(parser): rename parseData to parseInput`
3. `chore(deps): update lodash to v4.17.21`
4. `feat(parser): add JSON parsing support`

每个都可以独立验证、审查、回滚。

---

## 示例 4: 依赖升级

### Commit Plan

**Branch**: `chore/deps-axios`

**Baseline checks**:
- Command: `npm test && npm run build`
- Result: ✅ 全部通过

### Commits (ordered)

#### 1. `chore(deps): upgrade axios from 0.27 to 1.6`
- **Intent**: 升级 axios 依赖并修复 API 变更
- **Files**: `package.json`, `package-lock.json`, `src/api/client.ts`
- **Verification**: `npm test -- --grep "api"`
- **Notes**: 检查 breaking changes，更新调用方式

#### 2. `docs(deps): add axios migration note to CHANGELOG`
- **Intent**: 记录升级说明供团队参考
- **Files**: `CHANGELOG.md`
- **Verification**: 无
- **Notes**: 纯文档

---

## 示例 5: 文档与 CI 配置

### Commit Plan

**Branch**: `docs/readme-and-ci`

**Baseline checks**:
- Command: `npm test`
- Result: ✅ 通过

### Commits (ordered)

#### 1. `docs: add API usage examples to README`
- **Intent**: 补充 README 中的 API 使用示例
- **Files**: `README.md`
- **Verification**: 无
- **Notes**: 纯文档，不修改代码

#### 2. `ci: add GitHub Actions workflow for lint`
- **Intent**: 添加 PR 时的自动 lint 检查
- **Files**: `.github/workflows/lint.yml`
- **Verification**: 本地 `npm run lint` 通过
- **Notes**: 与文档改动分离，便于单独回滚

---

## 示例 6: 快速决策（单 commit vs 多 commit）

### 改动较少：单 commit 即可

```bash
# 仅修改 1 个文件，修复一个小 bug
fix(auth): correct token expiry validation
```

### 改动较多：必须拆分

| 改动类型 | 是否拆分 | 示例 |
|----------|----------|------|
| 格式化 + 功能 | ✅ 必须 | `style:` 与 `feat:` 分开 |
| 重命名 + 逻辑 | ✅ 必须 | `refactor:` 与 `feat:` 分开 |
| 多个独立模块 | ✅ 必须 | 按 scope 拆成多个 commit |
| 同一 bug 的多处修复 | ❌ 可合并 | 同一 `fix(scope):` |
| 仅文档更新 | ❌ 可合并 | 同一 `docs:` |
