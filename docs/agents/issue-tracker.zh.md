# Issue 跟踪器：GitHub

[English](issue-tracker.md) | 中文

本仓库的 issue 与规格说明以 GitHub issue 形式存在。所有操作使用 `gh` CLI（命令行界面）。

## 约定

- **创建 issue**：`gh issue create --title "..." --body "..."`。多行正文使用 heredoc。
- **读取 issue**：`gh issue view <number> --comments`，用 `jq` 过滤评论并同时获取标签。
- **列出 issue**：`gh issue list --state open --json number,title,body,labels,comments --jq '[.[] | {number, title, body, labels: [.labels[].name], comments: [.comments[].body]}]'`，按需加 `--label` 与 `--state` 过滤。
- **评论 issue**：`gh issue comment <number> --body "..."`
- **添加／移除标签**：`gh issue edit <number> --add-label "..."` / `--remove-label "..."`
- **关闭**：`gh issue close <number> --comment "..."`

在克隆内运行时 `gh` 会自动从 `git remote -v` 推断仓库。

## Pull Request 作为分诊入口

**PR（Pull Request）作为请求入口：否。** _（若本仓库把外部 PR 视为功能请求，改为 `yes`；`/triage` 读取此标志。）_

设为 `yes` 时，PR 与 issue 使用相同的标签和状态，通过对应的 `gh pr` 命令操作：

- **读取 PR**：`gh pr view <number> --comments`；查看差异用 `gh pr diff <number>`。
- **列出待分诊的外部 PR**：`gh pr list --state open --json number,title,body,labels,author,authorAssociation,comments`，只保留 `authorAssociation` 为 `CONTRIBUTOR`、`FIRST_TIME_CONTRIBUTOR` 或 `NONE` 的条目（丢弃 `OWNER`／`MEMBER`／`COLLABORATOR`）。
- **评论／打标签／关闭**：`gh pr comment`、`gh pr edit --add-label`／`--remove-label`、`gh pr close`。

GitHub 的 issue 与 PR 共享同一编号空间，裸的 `#42` 可能是两者之一——先用 `gh pr view 42` 判断，失败再退回 `gh issue view 42`。

## 当 skill 说「发布到 issue 跟踪器」时

创建一个 GitHub issue。

## 当 skill 说「获取相关工单」时

运行 `gh issue view <number> --comments`。

## Wayfinder 操作

供 `/wayfinder` 使用。**map（地图）** 是一个持有 **child（子）** issue 作为工单的单独 issue。

- **map**：一个带 `wayfinder:map` 标签的 issue，正文承载 Notes／Decisions-so-far／Fog。`gh issue create --label wayfinder:map`。
- **child 工单**：作为 GitHub sub-issue（`gh api` 调用 sub-issues 端点）挂到 map 上的 issue。sub-issue 不可用时，把 child 加进 map 正文的任务列表，并在 child 正文顶部写 `Part of #<map>`。标签：`wayfinder:<type>`（`research`／`prototype`／`grilling`／`task`）。被认领后，工单指派给驱动的开发者。
- **阻塞关系**：GitHub **原生 issue 依赖**——规范且在界面上可见的表示。加边：`gh api --method POST repos/<owner>/<repo>/issues/<child>/dependencies/blocked_by -F issue_id=<blocker-db-id>`，其中 `<blocker-db-id>` 是阻塞方的数字 **database id**（`gh api repos/<owner>/<repo>/issues/<n> --jq .id`，_不是_ `#number` 或 `node_id`）。GitHub 通过 `issue_dependencies_summary.blocked_by` 报告（只含未关闭的阻塞方——实时门禁）。依赖不可用时，退回 child 正文顶部的 `Blocked by: #<n>, #<n>` 行。所有阻塞方都关闭后工单才解除阻塞。
- **frontier 查询**：列出 map 的未关闭 child（`gh issue list --state open`，限定到 map 的 sub-issue／任务列表），剔除仍有未关闭阻塞方（`issue_dependencies_summary.blocked_by > 0`，或 `Blocked by` 行中有未关闭 issue）或已有指派者的条目；按 map 顺序取第一个。
- **认领**：`gh issue edit <n> --add-assignee @me`——会话的第一次写操作。
- **解决**：`gh issue comment <n> --body "<answer>"`，然后 `gh issue close <n>`，最后把上下文指针（gist 与链接）追加到 map 的 Decisions-so-far。
