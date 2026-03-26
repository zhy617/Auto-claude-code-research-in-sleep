# 03 NOVELTY CHECK

日期：2026-03-25  
对象：`BridgePreserve`

## 0. 方法摘要

`文档明确写到的`：  
`BridgePreserve` 的主张不是重新发明 expert merge 公式，而是认为多模态 MoE merge 失败的根因在于 `角色异质性 + bridge path 脆弱性`。因此需要先识别 expert 角色，只在同角色内 merge，并对 bridge experts 做显式保护。

`我的推断`：  
如果把这条线压成 contribution，它本质上是一个 `merge admissibility` 问题，而不是 `merge operator` 问题。

## 1. Core Claims

### Claim 1

**多模态 experts 不构成单一可比空间；merge 前必须先做 role partition。**

- Novelty：`MEDIUM`
- Closest prior work：`VEQ`、`MoME`、`Uni-MoE`
- 判断理由：
  - `文献明确支持`：VEQ 已明确写出 `modality discrepancy + expert heterogeneity`，MoME/Uni-MoE 也从结构上承认视觉与语言路径不同。
  - `我的推断`：真正相对新的地方不在“存在异质性”，而在“把异质性升级为 merge 的先验约束”。
- Reviewer 可能的质疑：
  - “这只是把大家早就知道的多模态异质性显式写进 merge pipeline。”
  - “这更像 apply X to Y：把 grouping 先验加到 MergeMoE/HC-SMoE 前面。”
- 结论：
  - 这个 claim 不能单独卖成高新颖度主张。

### Claim 2

**存在一类 `bridge experts`，它们对跨模态链路更关键，应该被显式保护。**

- Novelty：`MEDIUM-HIGH`
- Closest prior work：`SAMoE-VLA`、`FastMMoE`、`VEQ`
- 判断理由：
  - `文献明确支持`：已有工作支持多模态路由信号失配、视觉/文本不对称、专家功能差异。
  - `我的推断`：但“bridge experts”作为被保护的压缩对象，目前没有看到公开工作系统化提出。
- Reviewer 可能的质疑：
  - “bridge expert 只是你给某类关键 experts 起的新名字。”
  - “这仍然属于重要 expert protection，而不是新的方法问题。”
- 结论：
  - 这是全项目里最像真正 novelty 的 claim，但前提是你必须给出可操作定义。

### Claim 3

**在相同压缩率下，`same-role merge + bridge preservation` 比 `role-agnostic merge` 更能保住多模态能力。**

- Novelty：`MEDIUM`
- Closest prior work：`MergeMoE`、`HC-SMoE`、`PuzzleMoE`
- 判断理由：
  - `文献明确支持`：已有 merge 论文都在研究如何压缩，但默认聚类候选是可比的。
  - `我的推断`：若你能在多模态 setting 下证明“先约束谁能 merge”比“更优 merge 算子”更重要，这个 claim 才会变强。
- Reviewer 可能的质疑：
  - “这只是一个更好的 grouping heuristic。”
  - “收益是不是只来自减少 merge 次数，而不是 bridge-aware 机制本身？”

### Claim 4

**bridge-aware 保护带来的收益，不等价于单纯做 router calibration。**

- Novelty：`MEDIUM`
- Closest prior work：`Router KD`、`DenseMixer`
- 判断理由：
  - `文献明确支持`：Router KD 已经占住“压缩后性能掉是 router mismatch”这条解释线。
  - `我的推断`：你的增量在于声称有些结构损伤应该在 merge 前避免，而不是 merge 后再修。
- Reviewer 可能的质疑：
  - “naive merge + Router KD 是否就能达到类似结果？”
  - “你是不是只是做了一种更强的 router regularization？”

### Claim 5

**真正新的是“多模态 merge 的问题重定义”，而不是单一算子细节。**

- Novelty：`HIGH`
- Closest prior work：`MergeMoE`、`VEQ`、`SAMoE-VLA`
- 判断理由：
  - `文献明确支持`：现有工作分别重定义了 merge 目标、量化目标、routing 信号。
  - `我的推断`：还没有工作把“expert comparability failure in multimodal merge”作为主问题来写。
- Reviewer 可能的质疑：
  - “问题重定义必须带来强证据，否则只是 narrative innovation。”
  - “如果最终机制很轻，这会被看作 wording novelty 而非 method novelty。”

## 2. Closest Prior Work

| Paper | Year | Venue/Status | Overlap | Key Difference |
| --- | --- | --- | --- | --- |
| MergeMoE | 2025 | arXiv / OpenReview | 都研究 MoE expert merge 压缩 | 它优化 merge 目标；你质疑 merge 候选的可比性 |
| VEQ | 2026 | ICML / arXiv | 都承认 modality discrepancy 与 expert heterogeneity | 它做 quantization；你做 merge constraint |
| Router KD | 2026 | arXiv | 都关心压缩后退化与路由失配 | 它修 router；你想避免错误 merge 的结构伤害 |
| SAMoE-VLA | 2026 | arXiv | 都指出文本 MoE routing 迁移到多模态会出问题 | 它研究原生 routing 设计；你研究 merge setting |
| FastMMoE | 2025 | arXiv | 都利用 multimodal routing/冗余不对称 | 它做 expert activation reduction + token pruning，不做 merge |

## 3. Overall Novelty Assessment

- Score：`6.8 / 10`
- Recommendation：`PROCEED WITH CAUTION`

### 我为什么不给更高分

1. `多模态异质性存在` 这件事已经被多篇论文和你自己的笔记充分支持，不能算核心新意。
2. `role-aware` 很容易被降格为 heuristic grouping。
3. `Router KD` 会强力吸走“性能退化来自何处”的解释权。

### 我为什么也不判低分

1. `bridge expert` 作为被保护对象，目前还没有看到公开工作把它系统化地定义为 merge 主问题。
2. `merge admissibility` 这个 framing 目前仍然是空位。
3. 这条线仍牢牢属于 `expert merging for efficient inference`，不会完全偏题。

## 4. Reviewer Likely Objections

### objection 1

“这只是 `MergeMoE/HC-SMoE` 前面加了一个 grouping 步骤。”

- 风险等级：`高`
- 应对：
  - 证明 naive grouping 不够，bridge-sensitive 保护才是关键。
  - 证明收益不是更少 merge，而是更正确的 merge 单位。

### objection 2

“这属于把 `VEQ` 的异质性观察应用到 merge setting。”

- 风险等级：`中高`
- 应对：
  - 不再卖“发现异质性”，而卖“把异质性转成 merge admissibility constraint”。
  - 明确区分 quantization sensitivity 与 merge-induced bridge collapse。

### objection 3

“压缩后退化只是 router mismatch，`Router KD` 就够了。”

- 风险等级：`高`
- 应对：
  - 必须加入 `naive merge + Router KD` 的对照。
  - 如果你的方法在 bridge-sensitive 指标上仍更好，才站得住。

### objection 4

“bridge expert 没有严格定义，本质上是人为命名。”

- 风险等级：`最高`
- 应对：
  - 在论文里给出可操作 proxy，而不是语义描述。
  - 定义必须来自可复现实验信号，如跨模态共激活、文本/视觉 token 混合依赖、merge 后对跨模态任务的局部敏感度。

### objection 5

“真正新的是 empirical finding，不是 method。”

- 风险等级：`中`
- 应对：
  - 可以主动接受一部分：把论文定位成 `new problem setting + minimal method + strong diagnosis`。
  - 不必硬装成复杂算法论文。

## 5. Safe Positioning Suggestions

### 5.1 最安全的 contribution 表述

1. 我们指出多模态 expert merge 的关键失败点不是 merge 公式，而是错误的 expert comparability assumption。
2. 我们把 `bridge-sensitive experts` 作为应被优先保护的结构对象提出并操作化。
3. 我们给出一个最小 role-aware bridge-preserving merge 框架，并证明其收益不等价于 router-only calibration。

### 5.2 不建议使用的表述

1. “我们首次发现多模态 experts 存在异质性。”
2. “我们提出一种全新的 expert merge 算法。”
3. “我们彻底解决了多模态 merge 问题。”

### 5.3 更好的论文定位

`我的推断`：  
这条线最适合定位成：

- `new failure mode identification`
- `new problem formulation`
- `minimal but testable constraint-aware method`

而不是：

- `heavyweight merge operator paper`

## 6. 当前建议

- 总体建议：`建议重构后继续`
- 重构重点只有一个：
  - 先把 `bridge expert` 的操作化定义做硬，再谈方法复杂化。
- 如果这个定义做不硬：
  - 这条线会迅速从 `中高潜力题` 降为 `直觉上好听但不可 defend 的 heuristic paper`。
