请把下面这个 idea note 当作研究起点，帮我做一次“idea 初期 check + research plan”。
idea 文件路径："C:\Users\13183\OneDrive\Apps\Obsidian\EfficientAI\93_Research_Records\IdeaPipeline\2026-03-24\NOVELTY_IDEA_1.md"
`IDEA_NOTE_PATH`

请严格按下面的 pipeline 连续执行，不要中途停下来问我确认；如果信息不足，可以做合理假设，但必须明确标注哪些是“文档明确写到的”，哪些是“你的推断”。

你必须优先复用本仓库已有的 skill，按这个顺序执行：
1. `research-lit`
2. `paper-reading`
3. `novelty-check`
4. `experiment-plan`

我的核心目标有 4 个：
1. 搞清楚这个 idea 最相关的 related work 有哪些
2. 搞清楚可以参考的实验设置、baseline、metric、analysis 方式有哪些
3. 做 novelty check，判断哪些创新点可能会被 reviewer 质疑、argue、说成不新
4. 设计一个最小可行实验包，判断这个 idea 是否值得继续投入

请按以下阶段执行：

## Phase 0: Read and normalize the idea
先完整读取 `IDEA_NOTE_PATH`。
把内容整理成一个“研究命题卡片”，至少包含：
- 问题定义
- 核心假设
- 预期贡献
- 最强反对意见
- 需要验证的关键 claim
- 你认为当前描述中最模糊、最危险的点

输出到：
`./mystle/results/idea-check/{{date}}/00_IDEA_NORMALIZATION.md`

## Phase 1: Related-work mapping
使用 `research-lit` 的思路，围绕这个 idea 做 related-work 调研。
要求：
- 优先找最近 2-3 年最相关工作，同时补上必要奠基论文
- 不只是列论文，要按方法路线分组
- 明确哪些论文是“直接相关”，哪些是“相邻但可借鉴”
- 提炼 2-4 条最可能与我形成对比的主线
- 对每篇核心论文至少写清：问题、方法、核心机制、主要局限、与我 idea 的关系
- 特别总结它们常见的实验设置、数据集、baseline、metric、analysis 方式

输出文件：
`./mystle/results/idea-check/{{date}}/01_RELATED_WORK_MAP.md`

这个文件至少包含：
- 相关论文总表
- taxonomy / grouping
- 直接竞争论文 shortlist
- 可借鉴实验设置与 metric 归纳
- 目前文献中的空白与未充分回答的问题

## Phase 2: Deep reading of the closest papers
从 Phase 1 里选出 3-5 篇与你这个 idea 最接近、最危险、最值得精读的论文。
对每篇论文使用 `paper-reading` 的思路做结构化拆解。
要求：
- 重点拆它的 task、challenge、insight、novelty、potential flaw
- 特别关注：它到底解决了什么、没解决什么、它的实验设置为什么这样定
- 不要只复述摘要，要从“为什么这样设计”出发分析
- 最后汇总成一份跨论文对照总结

输出文件：
`./mystle/results/idea-check/{{date}}/02_CLOSEST_PAPERS_SYNTHESIS.md`

这个文件至少包含：
- 每篇论文的短析
- 跨论文对照表
- 哪些机制/实验设计最值得我复用
- 哪些点是我必须避开的“看似新但其实别人做过”的坑

## Phase 3: Novelty check
基于 `IDEA_NOTE_PATH` 的原始 idea 和前两阶段结果，执行 `novelty-check`。
请不要只判断“整体新不新”，而是拆成 3-5 个核心 technical claims 分别检查。
要求：
- 对每个核心 claim，给出 novelty 等级：HIGH / MEDIUM / LOW
- 找出 closest prior work
- 指出 reviewer 最可能的质疑方式，例如：
  - “这只是 X 的变体”
  - “这属于 apply X to Y”
  - “创新点在方法上不新，只是在 setting 上换了包装”
  - “真正新的是 finding，不是 method”
- 对每个高风险点，给出如何重构 contribution 或重新表述的建议

输出文件：
`./mystle/results/idea-check/{{date}}/03_NOVELTY_CHECK.md`

这个文件至少包含：
- Proposed method / idea summary
- Core claims
- Closest prior work
- Overall novelty assessment
- Reviewer likely objections
- Safe positioning suggestions

## Phase 4: Minimum experiment package
基于前三阶段结果，使用 `experiment-plan` 的思路，为这个 idea 设计“最小实验包”。
目标不是做完整论文实验，而是判断这个 idea 值不值得继续。
要求：
- 先冻结 1 个 primary claim，最多 1 个 supporting claim
- 设计最小可行实验，只保留最能提供信息增益的实验
- 明确：
  - 最小数据 / 任务设置
  - 最关键 baseline
  - 最关键 metric
  - 成功标准
  - 失败后该如何解释
  - 哪些实验是 must-run，哪些是 nice-to-have
- 如果这个 idea 当前还不适合直接做实验，也请明确说明卡点在哪里，并给出实验前必须补齐的前置问题

输出文件：
`./mystle/results/idea-check/{{date}}/04_MINIMUM_EXPERIMENT_PLAN.md`

这个文件至少包含：
- Claim map
- Main experiment block
- Novelty isolation block
- Failure analysis block
- Run order
- Go / No-Go criteria
- Biggest risk and mitigation

## Phase 5: Final briefing
最后整合前四阶段，给我一份简洁 briefing。

输出文件：
`./mystle/results/idea-check/{{date}}/05_FINAL_BRIEFING.md`

内容必须包括：
1. 这个 idea 当前最像哪一类已有工作
2. 它真正可能新的部分是什么
3. 哪些点最可能被 reviewer 质疑
4. 最值得先做的 1-2 个最小实验
5. 你的总体判断：`建议继续 / 建议重构后继续 / 暂不建议投入`

全局要求：
- 优先使用本仓库现有 skill 的流程与输出风格
- 明确区分“文献明确支持”与“你的推断”
- 尽量使用表格和对照总结
- 不要把重点放在泛泛 benchmark 排名上，要优先做机制、设定、claim、novelty、evidence 路径分析
- 如果可行，请优先读取本地 markdown / Obsidian / 已有笔记，再补 web 搜索
- 最后在聊天里只返回：
  - 产出文件清单
  - 你认为这个 idea 当前最大的 novelty risk 是什么
  - 你认为最值得先跑的一个最小实验是什么