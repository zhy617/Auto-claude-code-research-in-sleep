# NOVELTY_IDEA_3

日期：2026-03-24  
对应 idea：`RouteBridge`

## 0. Idea 简述

`RouteBridge` 的核心是：  
**在多模态 expert merge 之后，不再只做频率补偿或 logit 平均，而是引入模态条件摘要，在新的 latent routing space 中恢复 bridge-sensitive routing。**

## 1. 公开检索范围

- arXiv 搜索页：`multimodal mixture of experts`
- arXiv 搜索页：`vision-language mixture of experts`
- arXiv 搜索页：`expert merging mixture of experts`
- 检索重点：2024-2026 的 router、VLM/VLA routing、compression recovery、latent routing 几何

## 2. 最接近工作 Top-5

| 排名 | 工作 | 为什么接近 | 为什么还不一样 |
| --- | --- | --- | --- |
| 1 | Is Retraining-Free Enough? (`Router KD`, 2026) | 直接把压缩后的核心问题定位为 router-expert mismatch | 它主要蒸馏原始 router，不显式引入模态条件或 bridge-sensitive routing |
| 2 | SAMoE-VLA (2026) | 公开表明 token-based expert selection 在多模态/VLA 场景会失稳，因此改用 scene-level routing | 它研究的是原生 VLA 架构，不是 merge 后恢复 |
| 3 | CoVFT (2026) | 指出多模态上下文会改变视觉优化信号，支持“路由空间应当上下文化” | 它做的是视觉微调框架，不是 merge 后 router repair |
| 4 | L2R (`Low-Rank and Lipschitz-Controlled Routing`, 2026) | 直接改造路由空间几何，说明 router latent space 是可研究对象 | 它不是多模态，也不是 merge 后场景 |
| 5 | FusionRoute (2026) | 强调 router 可以承担一部分能力补偿职责 | 它更像 token-level collaboration，而不是 merge 后的条件化路由修复 |

## 3. 机制级差异

### 与频率/Logit 修复的差异

1. 传统修复默认原始 routing space 仍然可信，只是数值要重标定。
2. `RouteBridge` 的判断是：merge 之后，原始 routing space 本身已经因为多模态异质性而不再适合作为决策空间。
3. 因此修复重点应是“空间重写”，而不仅是“数值重标定”。

### 与原生多模态 routing 设计的差异

1. `SAMoE-VLA / CoVFT` 讨论的是原生多模态训练时该怎么路由。
2. `RouteBridge` 讨论的是：模型已经 merge 坏了一部分结构，如何低成本把 routing 判别性救回来。
3. 这使它天然更贴近你已有的 expert merging 主线。

### 与一般 router 改进论文的差异

1. 一般 router 论文追求整体路由质量提升。
2. `RouteBridge` 有一个更具体目标：优先恢复 bridge-sensitive routing。
3. 这让它和多模态 merge 的失败机理绑定得更紧。

## 4. Novelty 风险评级

**评级：中偏高**

### 降低风险的因素

- “merge 后 router 修复”本身是合理问题，且与你已有文本工作天然连续。
- 加入模态条件与 bridge 约束后，确实与纯文本线拉开了距离。

### 提高风险的因素

- router 方向近期工作非常多，容易让人觉得只是把多模态条件塞进已有 calibration 框架。
- 如果不把 `after merging` 的场景讲得很具体，它会被误归类为普通 routing paper。

## 5. 投稿叙事建议

### 推荐叙事

- 不要写成“更强 router”。
- 要写成：**现有 merge 后 router repair 方法默认文本式 routing 几何仍然可用，但多模态里这一假设失效；我们首次提出 merge-aware, modality-conditioned router repair。**

### 论文标题风格建议

- `RouteBridge: Modality-Conditioned Router Repair for Multimodal Expert Merging`
- `Repairing Routing Geometry after Multimodal MoE Merging`

### 最佳卖点

- “修的不是数值，而是 merge 后已经变形的多模态路由空间。”

## 6. 结论

- 这条线最适合做你现有文本 MoE 路由经验的延伸。
- 但它也是最容易被吸进“普通 router 改进论文池子”的一条。
- 作为第三候选很合理；作为主线则需要非常强地绑定 `after merging` 与 `bridge-sensitive routing` 两个关键词。
