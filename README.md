# H2Work

整个仓库是一个可直接安装的 h2work skill，用于跨项目、跨技术栈的软件工程工作流。根目录 [SKILL.md](SKILL.md) 按当前阶段引导读取开发、分支与 worktree、提交、审查、合并检查和选择性发布规范。

## 仓库结构

- [SKILL.md](SKILL.md)：h2work 的技能定义与流程路由。
- [agents/openai.yaml](agents/openai.yaml)：技能的界面元数据。
- references/：规范正文，按阶段读取。
- [pr skill](.agents/skills/pr/SKILL.md)：保留独立的 PR 正文模板，随仓库一起安装，由 h2work 显式读取。
- [AGENTS.md](AGENTS.md)：维护本仓库时使用的简短入口。

## 安装到个人技能目录

在 Codex 用户级 skills 目录中克隆整个仓库：

```bash
mkdir -p ~/.agents/skills
git clone https://github.com/somewan820/h2work.git ~/.agents/skills/h2work
```

保持完整目录结构，安装后可使用 `$h2work`。内置 pr skill 已包含在仓库中，无需另行安装即可按模板编写 PR 正文。

已有安装时，先核对本地修改，再通过 Git 更新所采用的版本；避免同时保留旧的 engineering-workflow 副本和新的 h2work 安装。

## 在项目中启用

在目标项目已有的 AGENTS.md 中合并以下入口，保留项目原有说明：

```markdown
进行开发、修复、重构、审查、验证、提交、PR、合并或发布工作时，
必须使用已安装的 h2work skill，按当前阶段读取并遵循对应规范。
PR 正文使用 h2work 内置的独立 pr skill。
```

需要随项目分发时，将完整技能目录放在目标项目的 `.agents/skills/h2work/` 下，不携带本仓库的 `.git` 元数据；将文件纳入项目版本管理，使新的 worktree 自带规范。

项目自己的构建、测试、运行环境、CI 必过任务、发布方式和授权范围继续由项目维护。具体操作以对应规范及项目已有授权为准，分支保护和 CI 规则需要在目标项目的平台中实际配置。

## 规范索引

- [工作流概念与项目上下文](references/overview.md)
- [开发与实现原则](references/standards/development.md)
- [Git 分支、worktree 与 PR 工作流](references/workflows/git.md)
- [代码提交信息规范](references/standards/commit-messages.md)
- [代码审查与合并准入](references/standards/code-review.md)
- [合并前检查规范](references/standards/pre-merge-checks.md)
- [按大需求选择性发布](references/workflows/release.md)

大需求与开发任务的定义、完整交付流程、尚未统一确定的事项集中维护在工作流概念与项目上下文中。
