# 00 IDEA NORMALIZATION

日期：2026-03-25  
Idea：`BridgePreserve`

## 0. 证据说明

- `文档明确写到的`：直接来自 `NOVELTY_IDEA_1.md` 原始 idea note。
- `本地笔记支持`：来自你已有的 `IDEA_LANDSCAPE.md`、`IDEA_REPORT.md`、Obsidian 笔记。
- `我的推断`：基于上述材料与公开论文摘要做出的研究判断，尚未由单篇论文直接证明。

## 1. 研究命题卡片

### 1.1 问题定义

- `文档明确写到的`：当前多模态 expert merge 失败的核心原因，不只是 merge 公式不够好，而是 `角色异质性 + bridge path 脆弱性`。
- `文档明确写到的`：因此不应把所有 experts 当成同一比较空间里的对象统一合并，而应先识别角色，只在同角色内 merge，并对 `bridge experts` 做显式保护。
- `我的推断`：这实际上把问题从“如何做更好的参数平均”改写成“哪些 experts 根本允许被比较与合并”。

### 1.2 核心假设

1. `文档明确写到的`：多模态 experts 不是单一同质集合，至少存在 `visual / language / bridge / shared` 一类角色差异。
2. `文档明确写到的`：bridge experts 对跨模态对齐与信息转译更关键，因此比普通 experts 更脆弱。
3. `本地笔记支持`：视觉侧 token 与 expert 使用更冗余，而文本与桥接路径更敏感；统一 merge 会掩盖这种不对称性。
4. `我的推断`：如果先做 role discovery，再限制 merge 候选集合，性能收益会主要体现在复杂跨模态任务保持率，而不是纯文本或简单感知题上。

### 1.3 预期贡献

1. `文档明确写到的`：提出 `role-aware bridge-preserving merge` 的问题定义，而不是新的平均公式。
2. `文档明确写到的`：把“多模态异质性”从背景现象提升为 merge 的硬约束。
3. `我的推断`：给出一个比 `role-agnostic merge` 更稳的最小机制链条：`role discovery -> same-role merge -> bridge protection -> optional router repair`。
4. `我的推断`：补上一类多模态 merge 论文最缺的分析证据：哪些专家承担桥接功能、哪些压缩真正破坏了跨模态链路。

### 1.4 最强反对意见

1. `文档明确写到的`：如果 `bridge experts` 只是经验命名而没有操作化定义，审稿人会说这是重新包装 heuristic。
2. `文档明确写到的`：如果最后方法只是“给某类 expert 更小 merge ratio”，新颖度会被压低。
3. `我的推断`：审稿人会质疑这是否只是把 `VEQ/Quant Experts` 里的异质性观察搬到了 merge setting。
4. `我的推断`：审稿人也可能说，这更像 `MergeMoE + Router KD + expert grouping` 的组合工程，而不是新的核心机制。

### 1.5 需要验证的关键 claims

| Claim ID | Claim | 当前状态 | 为什么关键 |
| --- | --- | --- | --- |
| C1 | 多模态 experts 不构成单一可比空间，先做 role partition 比直接 merge 更合理 | `推断` | 这是整条线是否成立的第一性前提 |
| C2 | 存在一类 `bridge experts`，其压缩敏感度显著高于普通 visual experts | `推断` | 这是“保护谁”的定义基础 |
| C3 | 在相同压缩率下，`same-role merge` 比 `role-agnostic merge` 保留更多跨模态能力 | `待实验` | 这是最核心、最容易被证伪的主 claim |
| C4 | bridge-aware 保护带来的收益，不等价于单纯做 router calibration | `待实验` | 这是防止论文被降级为 router repair 的关键 |
| C5 | 该收益主要出现在 bridge-sensitive 任务与路由/重建分析，而非任何 benchmark 都统一上涨 | `推断` | 这决定实验与论文叙事是否聚焦 |

### 1.6 当前最模糊、最危险的点

#### A. `bridge expert` 的定义仍然不硬

- `文档明确写到的`：当前 note 中强调了 bridge expert 重要，但尚未给出操作化定义。
- `我的推断`：若没有一个可以复现的 proxy，论文会从“问题定义创新”滑向“命名创新”。

#### B. `role discovery` 容易变成人为规则堆砌

- `文档明确写到的`：role-aware 若靠太多人为规则，论文会显得工程拼接。
- `我的推断`：需要一个最低限度可复现的发现路径，例如基于视觉/文本 token 路由比例、跨模态共激活、校准集上的输出敏感度，而不是手工读图命名专家。

#### C. 与已有工作的边界容易模糊

- `本地笔记支持`：`MergeMoE` 已经把 merge 提升到输出空间；`Router KD` 强调 router-expert mismatch；`VEQ/Quant Experts` 已公开承认多模态异质性。
- `我的推断`：如果你的贡献表达成“发现异质性 + 做保护”，审稿人会自然把它拆回这些既有工作。

## 2. 当前可接受的研究重述

### 2.1 推荐的一句话命题

`文档明确写到的`：文本 MoE merge 默认 experts 至少可在同一比较空间中被合并；而在多模态 MoE 中，这个假设因角色异质性与 bridge path 脆弱性而失效，因此 expert merging 应先做 role partition，并对 bridge experts 施加更严格的保护。  

### 2.2 更适合投稿的 thesis 版本

`我的推断`：  
不是“我们提出一个新的 merge 公式”，而是“我们指出多模态 MoE merge 的核心错误在于错误的可比性假设，并提出一个 role-aware、bridge-preserving 的最小机制框架来恢复正确的 merge 单位”。

## 3. 现在就该冻结的研究边界

1. 不把题目扩成“所有多模态压缩”。
2. 不把题目写成“又一个 router 修复方法”。
3. 不把题目卖成“发现所有专家语义角色的可解释系统”。
4. 主问题只保留一句：`哪些 experts 可以被合并，哪些不应该被合并？`

## 4. 当前阶段判断

- `结论`：这个 idea 目前最像“多模态 MoE merge 的问题重定义 + 最小约束机制”。
- `最大机会`：把多模态异质性从 observation 变成 merge constraint。
- `最大风险`：`bridge expert` 若无硬定义，整条线会被说成 heuristic repackaging。
