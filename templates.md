# Commit Prompt 模板

## Commit Plan 模板

写代码前必须输出此计划：

```markdown
## Commit Plan

**Branch**: `feature/<topic>` 或 `fix/<topic>`

**Baseline checks**:
- Command: `<验证命令>`
- Result: `<结果摘要>`

### Commits (ordered)

#### 1. `<type(scope): summary>`
- **Intent**: <一句话说明这次提交的唯一目的>
- **Files**: <预计涉及的文件/目录>
- **Verification**: <将运行的命令>
- **Notes**: <兼容性/风险/是否纯重构等>

#### 2. `<type(scope): summary>`
- **Intent**: ...
- **Files**: ...
- **Verification**: ...
- **Notes**: ...

(继续直到覆盖所有改动)
```

---

## Commit Record 模板

每完成一个 commit 后输出：

```markdown
## Commit Record

**Commit**: `<hash短码>` `<message>`

**Why**: <为什么需要这次提交，以及它与前后提交的边界>

**Verification**:
- Command: `<命令>`
- Result: `<关键结果>`

**Push**: <是否已 push；如果没 push，说明何时 push 与原因>
```

---

## PR 描述模板

最终产出的 PR 描述草稿：

```markdown
## Summary
<改了什么，用户可见变化>

## Scope
<模块列表，对应 commit 范围>

| Commit | Type | Scope | Description |
|--------|------|-------|-------------|
| abc123 | feat | auth  | Add JWT validation |
| def456 | test | auth  | Add auth unit tests |

## Verification
<你跑过哪些验证>

- [ ] Unit tests: `npm test` ✅
- [ ] Lint: `npm run lint` ✅
- [ ] Build: `npm run build` ✅
- [ ] Manual testing: <描述>

## Risk & Rollback

### Risks
- <风险点 1>
- <风险点 2>

### Rollback Strategy
- 回滚哪些 commits: `git revert <hash1> <hash2>`
- 数据迁移: <是否有，如何处理>
- 服务依赖: <是否影响其他服务>

## Screenshots / Logs
<如适用，附上截图或关键日志>
```

---

## Commit Message 模板

### 标准格式

```
<type>(<scope>): <imperative summary>

<optional body - 解释 why 而非 what>

<optional footer>
```

### 带 Breaking Change

```
feat(api): change authentication endpoint

BREAKING CHANGE: /auth/login now requires email instead of username

Migration:
- Update client calls to use email field
- Run migration script: `npm run migrate:auth`
```

### 带 Issue 引用

```
fix(parser): handle empty input gracefully

Closes #123
```

---

## Verification 命令参考

根据项目类型选择合适的验证命令：

### Node.js / TypeScript
```bash
npm test                    # 单元测试
npm run lint               # 代码检查
npm run build              # 构建
npm run typecheck          # 类型检查
```

### Python
```bash
pytest                     # 单元测试
ruff check .              # 代码检查
mypy .                    # 类型检查
python -m build           # 构建
```

### Rust
```bash
cargo test                 # 单元测试
cargo clippy              # 代码检查
cargo build --release     # 构建
```

### Go
```bash
go test ./...             # 单元测试
golangci-lint run         # 代码检查
go build ./...            # 构建
```
