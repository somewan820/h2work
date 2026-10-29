# Git 分支、worktree 与 PR 工作流

适用于日常开发任务，大需求与开发任务的定义见 [工作流总览](../overview.md)。每个任务从最新 dev 创建独立分支和独立 worktree，通过 PR 合入 dev，最终在 dev 中保留一个提交。

本文的分支基线和 PR 目标适用于开发任务；大需求的发布分支和发布 PR 遵循 [按大需求选择性发布](release.md)。

## 分支约定

- main、dev 是长期受保护分支，所有变更通过 PR 合入。
- 任务代码在独立任务分支上开发和提交，不在 main、dev 上直接开发或推送。
- 禁止强制推送或删除 main、dev，也不能通过 rebase 改写这两个分支的历史。
- 一个开发任务对应一个独立分支、一个独立 worktree 和一个 PR；分支与 worktree 只承载该任务的改动。
- 开发过程中可以保留多次提交，合入 dev 时统一使用 Squash and merge，压缩成一个提交。

## 任务分支命名

- 开发任务分支统一使用 feat/任务编号-简述，例如 feat/TASK-123-add-login。
- 任务编号沿用项目中的任务标识，简述表达该任务的主要改动。

## 开始开发任务

1. 检查当前仓库、分支、工作区和 git worktree list，保留已有改动，确认本任务对应的分支和 worktree。继续已有任务时，复用该任务的分支、worktree 和 PR。
2. 确认目标仓库的远端。以下命令以 origin 为例，实际名称不同时替换为对应远端。
3. 执行 git fetch origin，获取远端最新状态。
4. 新任务从 origin/dev 创建新分支，并在独立目录创建检出该分支的 worktree。不能从过期的本地 dev、其他未合并任务分支或已完成任务分支继续开发。
5. 在任务 worktree 中检查当前目录和分支。新建 worktree 应当干净；继续已有任务时，确认并保留该任务尚未提交的改动。管理工具创建的 worktree 如果处于 detached HEAD，必须先创建并切换到本任务分支。
6. 按 [合并前检查规范](../standards/pre-merge-checks.md) 尽早确定本任务的检查清单，明确适用范围、项目已有命令和通过标准。

创建新任务的命令示例，执行前替换分支名与目录：

```bash
git fetch origin
git worktree add -b "feat/<任务编号>-<简述>" "<独立目录>" origin/dev
```

## worktree 与并行开发

- 无论串行还是并行，每个任务都必须使用独立 worktree。主工作区用于仓库维护和 worktree 管理，任务代码在对应的任务 worktree 中修改。
- 不同任务使用各自的目录与分支，不在同一目录交替切换多个任务。
- 编辑文件、运行命令、提交、rebase 和推送时，显式使用本任务的 worktree 路径作为工作目录，操作前确认当前分支。
- 保留其他任务的文件和工作状态，不切换、重置或清理其他任务的分支和 worktree。
- worktree 共享仓库的提交历史和分支信息。同步本任务时，仍需遵循下文的 rebase 和 PR 合并要求。
- 并行运行服务或会写入状态的测试时，为各任务分配独立端口，并隔离会互相影响的测试数据、数据库命名空间等资源；worktree 本身不隔离这些运行资源。
- 清理 worktree 时，先确认对应 PR 已合并，并保存其中仍需保留的未提交、未跟踪和忽略文件；使用环境提供的 worktree 管理工具或 git worktree remove，仅清理本任务的 worktree。

## 提交 PR

1. 完成本任务的开发，按 [代码提交信息规范](../standards/commit-messages.md) 整理提交，确保工作区干净。
2. 再次执行 git fetch origin，在任务分支上执行 git rebase origin/dev。
3. 遇到冲突时，结合双方代码意图解决后继续 rebase；无法判断业务意图时，保留现场并说明具体冲突。
4. 检查相对 origin/dev 的提交和差异，确保只包含本任务内容。
5. 按 [合并前检查规范](../standards/pre-merge-checks.md) 对 rebase 后的代码执行适用验证，记录已执行项和待完成项；独立审查在 PR 创建后按下文完成。
6. 显式指定远端和本任务分支进行推送。
7. 使用仓库内的 [pr skill](../../.agents/skills/pr/SKILL.md) 编写 PR 正文，在 Summary 中关联开发任务和所属大需求，创建目标分支为 dev 的 PR。

同步 dev 统一使用 rebase，不通过 merge dev 或 merge origin/dev 产生同步用的 merge commit。

已推送的任务分支在 rebase 后需要更新远端历史时，先核对远端是否存在他人新增提交。确认不会覆盖他人工作后，使用 --force-with-lease；如果推送被拒绝，重新检查远端历史，不能改用 --force。

## 更新和合并 PR

1. 在现有任务分支和 PR 上处理审查意见，并按 pr skill 更新正文，使其反映最终改动和验证结果。
2. 合并前再次获取最新 dev；如果 dev 已前进，将任务分支重新 rebase 到最新 origin/dev，重新检查差异并验证，再更新 PR。
3. 按 [代码审查与合并准入](../standards/code-review.md) 完成独立交叉验证与对抗性审查。
4. 按 [合并前检查规范](../standards/pre-merge-checks.md) 核对适用必过项和证据，确认审查结论与检查结果对应最终准备合并的代码。
5. 按 [代码提交信息规范](../standards/commit-messages.md) 检查最终 Squash 提交信息，通过代码托管平台的 Squash and merge 合入 dev，确保本 PR 最终只产生一个任务提交。

提交 PR 前的 rebase 用于同步最新 dev，合并 PR 时的 squash 用于压缩任务提交。任务合入 dev 后，记录其所属大需求、任务标识、PR 和最终 squash 提交 SHA，供发布时选择。

## 完成标准

- 本任务具有独立分支和独立 worktree，开发与验证均在对应 worktree 中完成。
- 提交 PR 及合并前，任务分支包含最近一次获取的 origin/dev，可通过 git merge-base --is-ancestor origin/dev HEAD 验证。
- 合并前，相对 dev 的提交与文件差异只包含本任务内容。
- PR 的目标分支为 dev，独立审查和项目要求的检查均满足合并条件。
- 合并后，dev 上本 PR 对应一个压缩提交，没有新增 merge commit。

## 代码托管平台配置

- 为 main、dev 配置分支保护：要求通过 PR 变更，独立审查通过后才能合并，禁止强制推送和删除。
- 将项目明确的必过 CI 检查配置到分支保护中，确保检查结果对应待合并代码。
- 为 dev 要求线性提交历史，并将合入 dev 的 PR 合并方式限定为 Squash and merge。
- 文档用于指导 agent；分支保护和合并限制需要在各项目的代码托管平台实际配置。
