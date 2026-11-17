# Agent Prompt Kit

两个月指挥 Agent 写代码，沉淀下来最好用的 10 条 Prompt，做成了 skill。装上之后输入 `/pk`，就能一键调出。

每条 skill 都保留原话，同时把它展开成 Agent 能照着执行的步骤和输出格式。这样效果更稳定，不靠模型临场发挥。

## 安装

```bash
npx skills add aircrushin/agent-prompt-kit
```

支持 Claude Code、Cursor、Codex、Copilot、Cline 等兼容 Agent Skills 标准的工具。只想装其中一条：

```bash
npx skills add aircrushin/agent-prompt-kit --skill pk-red-team
```

## 用法

在 Claude Code 里输入 `/pk`，会弹出全部快捷指令：

| # | 快捷指令 | 原始 Prompt | 什么时候用 |
|---|---|---|---|
| 1 | `/pk-first-principles` | 从第一性原理出发，找到最本质原因，修复一下 | 修 bug，不想要补丁 |
| 2 | `/pk-align-3plans` | 先问不清晰的问题对齐喜好，再出 3 版方案 | 需求和审美偏好还模糊 |
| 3 | `/pk-reframe` | 不要默认我的问题定义是对的 | 怀疑自己问错了问题 |
| 4 | `/pk-ripple` | 推演二阶、三阶影响 | 合并影响面大的改动之前 |
| 5 | `/pk-cross-domain` | 其他领域有没有类似方案 | 常规思路走到头了 |
| 6 | `/pk-red-team` | 假设你的方案是错的，站在最强反对者角度推翻它 | 拍板重要方案之前 |
| 7 | `/pk-git-archaeology` | 分析 Git 历史，识别可以安全移除的历史包袱 | 打算清理老代码 |
| 8 | `/pk-onboarding-cost` | 假设明天有个强但不了解项目的工程师来接手 | 准备交接，或代码越来越难读 |
| 9 | `/pk-pr-bottleneck` | 找出最近 N 天最慢的 PR，看卡在哪里 | 交付变慢 |
| 10 | `/pk-retro` | 复盘 Bug、返工、回滚，沉淀成规则、自动化或 Skill | 同样的问题反复出现 |

可以带参数：

```
/pk-first-principles 登录后偶发白屏
/pk-ripple 把 session 从内存挪到 Redis
/pk-pr-bottleneck 14
```

不带参数时，就作用于当前对话里正在讨论的问题。

### 总入口：用编号和组合

```
/prompt-kit            # 显示菜单
/prompt-kit 6          # 对当前方案执行第 6 条
/prompt-kit 3 2 4 6    # 按顺序串起来执行
```

推荐组合：

- **做新功能**：`3 → 2 → 4 → 6`
- **疑难 bug**：`1 → 6`
- **卡在设计上**：`3 → 5 → 6`
- **接手老项目**：`8 → 7`
- **团队效能体检**：`9 → 10`

### 其他 Agent

不支持斜杠命令的 Agent，会根据描述自动触发。直接说"用 pk-red-team 审一下这个方案"，或者说原话就行。

### 不用 skill？

[PROMPTS.md](./PROMPTS.md) 里是 10 条纯文本版，可以导入 Raycast Snippets、TextExpander 或输入法短语，打 `pk1` 到 `pk10` 自动展开。

## English

Ten battle-tested prompts for steering coding agents (first principles, align-then-3-plans, reframe the problem, 2nd/3rd-order effects, cross-domain analogies, red-team your own plan, git archaeology, onboarding cost, PR bottlenecks, root-cause retro). Install with `npx skills add aircrushin/agent-prompt-kit`, then type `/pk` in Claude Code. Skill bodies are in Chinese; the agent will answer in your language.

## License

MIT
