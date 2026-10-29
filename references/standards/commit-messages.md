# 代码提交信息规范

开发任务的普通提交和最终合入 dev 的 Squash 提交采用 Conventional Commits 结构，使用中文说明，并关联任务与所属大需求。发布时 cherry-pick 的任务提交保留这些信息。

基础结构遵循 [Conventional Commits 1.0.0](https://www.conventionalcommits.org/en/v1.0.0/)。摘要中的任务编号与 Requirement footer 是本工作流增加的关联约定。

## 提交格式

```text
<type>(<scope>): <中文简述> [任务编号]

可选正文：说明改动原因、关键取舍或迁移要求。

Requirement: <所属大需求编号>
```

- type 使用小写英文；scope 可选，使用项目已有的模块或业务域标识，如 user、order、gateway。
- 摘要和正文使用简体中文，代码标识符、技术名称与任务编号保留原有写法。
- 摘要以具体行为说明本次改动，例如“修复订单取消后库存未恢复”。
- 任务编号放在摘要末尾的方括号中，Requirement 放在 footer 中；沿用项目已有的真实任务与大需求编号。
- 正文可省略；确需补充原因、取舍、影响或迁移要求时填写，与摘要、footer 之间各留一个空行。

示例：

```text
feat(user): 新增用户标签查询接口 [TASK-123]

Requirement: REQ-100
```

省略 scope 的示例：

```text
docs: 补充本地开发环境说明 [TASK-124]

Requirement: REQ-100
```

## 提交类型

- feat：新增功能或对外能力。
- fix：修复错误行为。
- refactor：重构代码结构，保持既有行为。
- perf：性能优化。
- docs：文档或说明变更。
- test：新增或调整测试。
- build：构建、依赖或打包变更。
- ci：持续集成、持续交付配置变更。
- chore：不属于以上类别的维护工作。
- revert：回退已有提交；正文说明原因并关联被回退的提交。

根据实际改动选择类型。分支名以 feat/ 开头，不限定其中提交的类型。

## 提交粒度与内容

- 编写提交信息前，检查 git status 和本次准备提交的 diff，依据真实改动归纳类型、范围和摘要。
- 一次提交围绕一个完整改动，功能实现和必要测试可以一起提交。
- 一个开发任务允许多次提交，各次提交分别说明实际完成的内容。
- PR 正文使用 pr skill；提交正文用于解释代码改动，不复制整份 PR 模板。

## 最终 Squash 提交

- 合并开发任务 PR 前，根据相对 dev 的最终完整差异整理提交信息，概括整个任务的交付结果。
- 最终摘要包含任务编号，footer 包含所属大需求编号，并保留任务涉及的破坏性变更与迁移说明。
- 合并后检查 dev 上实际生成的提交信息，并将最终提交 SHA 记录到对应任务或 PR，供发布清单引用。
- cherry-pick 到发布分支时，保留源任务的摘要、任务编号、Requirement 和破坏性变更说明，同时保留来源提交记录。

## 破坏性变更

使用 ! 标记破坏性变更，并通过 BREAKING CHANGE footer 说明影响和调用方的迁移方式。该标记适用于任何提交类型。

```text
feat(user)!: 调整用户资料返回结构 [TASK-125]

Requirement: REQ-100
BREAKING CHANGE: nickname 字段改为 displayName，调用方需同步调整。
```
