---
name: pk-pr-bottleneck
description: PR 瓶颈分析：全面分析最近 N 天的所有 PR，找出最慢的几个，定位卡在哪个环节以及可以优化的地方。Use when the user types /pk-pr-bottleneck, asks "分析最近的 PR / 哪些 PR 最慢 / 交付为什么卡", or wants to improve PR cycle time and delivery flow.
argument-hint: "[天数，默认 30]"
---

# 09 · 最慢的 PR 卡在哪里

> 原始指令：全面分析最近 XX 天里的所有 PR，找出最慢的那几个，看看有没有导致卡顿、可以优化的地方。

**时间窗口：** $ARGUMENTS
（如果这里为空或显示为字面量 `$ARGUMENTS`，默认取最近 30 天，并在报告开头写明。）

## 执行步骤

1. **拉数据。** 优先用 `gh`：
   ```bash
   gh pr list --state merged --search "merged:>=YYYY-MM-DD" --limit 500 \
     --json number,title,author,createdAt,mergedAt,additions,deletions,changedFiles,commits,reviews,labels
   ```
   对最慢的那几个，再用 `gh pr view <N> --json commits,reviews,comments,statusCheckRollup` 看细节。
   没有 `gh` 或没有远端时，退回到 `git log --merges --since=...` 和分支的首个提交时间，并注明数据精度有限。
2. **把每个 PR 拆成阶段并算时长：**
   首个提交 → 开 PR → 首次评审 → 最后一次批准 → 合并。另外统计：评审轮数、CI 时长和失败次数、改动规模（行数、文件数）。
3. **找出最慢的那几个**（按总周期排，默认取前 5 个，或者最慢的 10%），逐个说明**卡在哪个阶段、为什么**：
   - 等评审太久（评审人太少？分配不明确？）
   - 来回改了很多轮（需求不清？缺设计对齐？）
   - CI 慢或经常挂（不稳定的测试？构建太慢？）
   - PR 太大（应该拆分？）
   - 被其他 PR 或依赖阻塞
4. **找共性**：慢的 PR 有没有共同点，比如同一个模块、同一种改动类型、同一个评审人、同一个时间段。
5. **给优化建议**：每条建议都要对应到具体数据，并估计能省多少时间。

## 输出格式

- 总览：PR 数量、周期中位数 / P90、各阶段耗时占比
- 最慢 PR 表：| PR | 标题 | 总周期 | 最慢阶段 | 原因 | 证据 |
- 共性瓶颈
- 优化建议（按预计收益排序）

## 说明

这里的"慢"默认指 PR 交付周期。如果用户其实想找**让产品运行变慢的 PR**（性能回退），就改为排查这段时间内对热点路径、查询、依赖、打包体积的改动，并用基准测试或 profiling 证实。
