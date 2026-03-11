# Commit Prompt

遵循 Git 工程流程的 Agent Skill，用于将大量代码更新拆分成模块化原子提交，生成 Commit Plan、逐步验证并输出 PR 描述草稿。支持 Cursor、Codex、OpenCode、Claude Code 等 AI 编程助手。

## 功能特性

- **原子提交**：将大改动拆分为单一职责、可独立验证的小提交
- **Commit Plan**：写代码前先规划，明确每个提交的意图与验证方式
- **Conventional Commits**：统一使用规范的 commit message 格式
- **PR 描述**：自动生成包含验证清单、风险与回滚策略的 PR 草稿

## 适用场景

- 提交代码、创建 PR
- 需要拆分大量改动
- 需要规范 commit 流程
- 提到 commit、push、pull request 相关操作时

## 安装

### 方式一：npx（推荐）

使用 [add-skill](https://add-skill.org/) 一键安装，支持 Cursor、Codex、OpenCode、Claude Code 等多种 AI 编程助手：

```bash
# 安装到当前项目
npx add-skill qCanoe/commit-prompt-skill

# 安装到用户目录（全局可用）
npx add-skill qCanoe/commit-prompt-skill -g

# 非交互模式（CI/CD）
npx add-skill qCanoe/commit-prompt-skill -g -y
```

### 方式二：手动安装

将本仓库克隆或复制到对应 agent 的 skills 目录：

```bash
# Cursor: ~/.cursor/skills/ 或 .cursor/skills/
git clone https://github.com/qCanoe/commit-prompt-skill.git ~/.cursor/skills/commit-prompt

# Codex: ~/.codex/skills/
git clone https://github.com/qCanoe/commit-prompt-skill.git ~/.codex/skills/commit-prompt
```

## 核心原则

### 原子提交要求

- **单一职责**：每个 commit 只做一件事（one logical change）
- **可构建**：每个 commit 后代码处于可运行状态
- **可验证**：必须附带验证证据（tests/lint/build 结果摘要）
- **可读性**：单个 commit 的 diff ≤300 行（超出须说明理由）

### 禁止事项

- ❌ 不相关改动混在同一个 commit（格式化、重命名、依赖升级、功能改动）
- ❌ 一次性巨型 commit
- ❌ 边改结构边加功能

## 工作流程

### Phase 1: 准备

1. 创建分支：`feature/<topic>` 或 `fix/<topic>`
2. 同步主线：`git fetch --all --prune` 后 `git rebase origin/main`
3. 基线验证：运行 tests + build + lint，记录输出摘要

### Phase 2: 规划

写代码前必须先输出 **Commit Plan**。规划原则：

- 先 infra/refactor 再 feature
- 先添加测试再改逻辑
- 格式化全仓单独 commit，放最后或最开始

### Phase 3: 执行

每个模块循环：

1. 实现改动
2. 运行该模块验证命令
3. `git add -p` 精确暂存（禁止 `git add .`）
4. commit（使用 Conventional Commits 格式）
5. 输出 Commit Record
6. push（或批量 push，但每个 commit 都要可验证）

### Phase 4: PR

最终输出 PR 描述草稿（含 Summary、Scope、Verification、Risk & Rollback）。

## Commit Message 格式

使用 [Conventional Commits](https://www.conventionalcommits.org/)：

```
type(scope): imperative summary

[optional body]

[optional footer]
```

**类型**：`feat` | `fix` | `refactor` | `chore` | `test` | `docs` | `build` | `ci` | `perf` | `style`

## 文件结构

```
commit-prompt/
├── SKILL.md      # Skill 定义与核心逻辑
├── templates.md  # Commit Plan、Commit Record、PR 描述模板
├── examples.md   # 完整示例（含用户认证、Bug 修复、格式化重构）
└── README.md     # 本说明
```

## 模板与示例

- 完整模板：[templates.md](templates.md)
- 具体示例：[examples.md](examples.md)

## 许可证

MIT

## 贡献

欢迎提交 Issue 和 Pull Request。
