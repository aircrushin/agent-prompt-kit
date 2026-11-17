---
name: pk-git-archaeology
description: Git 考古：分析整个代码库的 Git 历史、关键 PR 和架构演变，区分有意设计、历史遗留和为已消失问题引入的代码，找出可以安全移除的历史包袱。Use when the user types /pk-git-archaeology, asks "分析 Git 历史 / 哪些是历史包袱 / 哪些可以删", or plans a cleanup or refactor of a legacy codebase.
argument-hint: "[目录或模块，可选]"
---

# 07 · Git 考古：找出可移除的历史包袱

> 原始指令：深入分析整个代码库的 Git 历史、关键 PR 和架构演变过程。找出哪些设计是有意为之，哪些是历史遗留，哪些是为了解决已经不存在的问题而引入的。识别可以安全移除的历史包袱。

**范围：** $ARGUMENTS
（如果这里为空或显示为字面量 `$ARGUMENTS`，就分析整个仓库。）

## 执行步骤

1. **画演变时间线。** 常用命令：
   - `git log --reverse --format='%h %ad %an %s' --date=short | head -200`：项目早期
   - `git log --shortstat --format='%h %ad %s' --date=short`：找大重构（改动行数很大的提交）
   - `git log --grep -iE 'revert|migrate|refactor|deprecat|workaround|hack|temp'`：找关键转折点
   - `git log --diff-filter=D --name-only`：被删掉的模块
   - 如果有 `gh`：`gh pr list --state merged --limit 200 --json number,title,mergedAt,body`，读关键 PR 的描述和讨论
2. **对重点设计追根溯源：** 用 `git log -S '<符号>'`、`git log --follow <文件>`、`git blame -w -C -C <文件>` 找到引入它的那次提交和 PR，读当时的提交说明，弄清**当初为什么这么做**。
3. **给每个重点设计分类：**
   - **有意为之**：理由今天依然成立
   - **历史遗留**：当初是权宜之计，一直没人清理
   - **为已消失的问题而引入**：原来的问题已经不在了。必须给出"已消失"的证据，例如依赖已升级到修复版本、功能已下线、平台限制已取消、调用方已删除、要兼容的旧版本已不再支持、功能开关已永久打开。
4. **评估移除是否安全：** 有没有调用方、有没有测试依赖、有没有外部或运行时引用（反射、配置、字符串拼接、对外 API）。

## 输出格式

| 设计/代码 | 位置 | 引入的 commit/PR | 当初原因 | 现状 | 分类 | 能否移除 | 风险 | 怎么验证 |
|---|---|---|---|---|---|---|---|---|

然后给**移除计划**：按收益/风险比排序，分批进行，每批写清验证方式和回滚方式。

## 原则

- 只做分析，**不要直接删代码**，等用户确认之后再动手。
- 每条结论都要附 commit hash 或 PR 号。找不到证据的标注为「推测」。
