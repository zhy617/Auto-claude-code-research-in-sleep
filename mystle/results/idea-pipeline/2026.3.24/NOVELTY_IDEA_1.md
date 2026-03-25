# NOVELTY_IDEA_1

日期：2026-03-24  
对应 idea：`BridgePreserve`

## 0. Idea 简述

`BridgePreserve` 的核心不是再发明一个更聪明的平均公式，而是提出：  
**多模态 expert merge 失败的核心原因在于“角色异质性 + bridge path 脆弱性”，因此应该先识别专家角色，只在同角色内 merge，并对 bridge experts 做显式保护。**

## 1. 公开检索范围

- arXiv 搜索页：`multimodal mixture of experts`
- arXiv 搜索页：`vision-language mixture of experts`
- arXiv 搜索页：`expert merging mixture of experts`
- 已公开可见的条目重点覆盖 2024-2026

## 2. 最接近工作 Top-5

| 排名 | 工作 | 为什么接近 | 为什么还不一样 |
| --- | --- | --- | --- |
| 1 | VEQ (`Modality-Adaptive Quantization for MoE Vision-Language Models`, 2026) | 它直接把 `modality discrepancy` 与 `expert heterogeneity` 作为多模态压缩问题的核心 | 它做的是量化，不是 expert merging；也没有提出 bridge expert 保护机制 |
| 2 | Quant Experts (`Token-aware Adaptive Error Reconstruction`, 2026) | 也在做 VLM 压缩，并明确强调重要通道会随模态与 token 改变 | 它处理的是量化补偿，不是角色约束下的 merge 策略 |
| 3 | MergeMoE (`Expert Output Merging`, 2025/2026) | 它是目前很接近你主线的 merge 方法，而且把问题提升到输出空间 | 它默认 expert 可比较，没有把多模态角色异质性作为一级对象 |
| 4 | Is Retraining-Free Enough? (`Router KD`, 2026) | 它指出压缩后的关键问题是 router-expert mismatch | 它关注的是 router 校准，而不是“哪些 expert 根本不该被合并” |
| 5 | SAMoE-VLA (2026) | 它公开表明 token-level routing 直接迁移到多模态/VLA 会失效，说明多模态 route signal 的确不同 | 它研究的是原生 routing 设计，不是 merge 后的角色保护压缩 |

## 3. 机制级差异

### 与现有 merge 工作的差异

1. 现有 merge 论文大多默认：专家之间虽然不同，但至少在同一比较空间中。
2. `BridgePreserve` 的第一性判断是：**多模态专家并不构成单一比较空间**。
3. 因而它的第一步不是 merge，而是 `role discovery / role partition`。

### 与现有 multimodal compression 工作的差异

1. 现有多模态压缩大多聚焦 `quantization`、`token pruning`、`projector compression`、`routing redesign`。
2. 它们承认模态异质性，却很少把它写成 `expert merging constraint`。
3. `BridgePreserve` 的新意，是把“异质性”从背景现象升级为 merge 的硬约束。

### 与 router repair 线的差异

1. Router KD、DenseMixer、SAMoE-VLA 都是在修路由。
2. `BridgePreserve` 的立场更强：先别把关键 bridge path 合并掉，修路由是第二步。
3. 这使它更像“多模态 merge 的问题重定义”，而不是单点补丁。

## 4. Novelty 风险评级

**评级：中**

### 降低风险的因素

- 公开工作已经充分证明多模态压缩存在模态异质性与 router mismatch，但还没有看到“bridge-preserving merge”被系统化成主问题。
- 你的主线仍然牢牢站在 `expert merging for faster inference` 上，不会被认为完全偏题。

### 提高风险的因素

- 如果“bridge experts”只是经验命名、没有操作化定义，审稿人会说这是重新包装的 heuristic。
- 如果最终做法只是“给某类专家更小 merge ratio”，新颖度会被压低。

## 5. 投稿叙事建议

### 推荐叙事

- 不要讲成“我们发明了一个新的 merge formula”。
- 要讲成：**文本 MoE merge 的核心假设在多模态里首次失效，因为专家角色不再同质；我们提出 role-aware bridge-preserving merge 作为新的问题定义与方法框架。**

### 论文标题风格建议

- `BridgePreserve: Role-Aware Expert Merging for Multimodal MoE Models`
- `Not All Experts Are Comparable: Bridge-Preserving Merging for Multimodal MoEs`

### 最佳卖点

- “不是再做一次更精细的平均，而是重新定义什么 expert 才允许被合并。”

## 6. 结论

- 这条 idea 目前没有明显撞上公开同题工作。
- 最大风险不是“已有论文做过”，而是“概念太好听但定义不够硬”。
- 如果后续两周能把 `bridge expert` 的可操作定义讲清楚，这条线很有机会成为主线题目。
