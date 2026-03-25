# NOVELTY_IDEA_2

日期：2026-03-24  
对应 idea：`ViTAL-Merge`

## 0. Idea 简述

`ViTAL-Merge` 的核心是：  
**多模态 expert merge 不应采用统一压缩率，而应依据视觉 token 冗余、语言 token 稀缺性以及 bridge path 重要性，对 merge budget 做模态不对称分配。**

## 1. 公开检索范围

- arXiv 搜索页：`multimodal mixture of experts`
- arXiv 搜索页：`vision-language mixture of experts`
- arXiv 搜索页：`expert merging mixture of experts`
- 检索重点：2024-2026 的 VLM/MLLM 压缩、剪枝、量化、路由与 MoE 结构工作

## 2. 最接近工作 Top-5

| 排名 | 工作 | 为什么接近 | 为什么还不一样 |
| --- | --- | --- | --- |
| 1 | QMoP (`Query Guided Mixture-of-Projector`, 2026) | 直接围绕视觉 token 压缩做动态选择，且明确以 VLM 推理开销为目标 | 它压的是 projector/visual tokens，不是 expert merge budget |
| 2 | POP (`Online Structural Pruning`, 2026) | 强调上下文相关的动态结构剪枝，覆盖 VLM/MoE | 它是在线剪枝，不是静态 merge 的预算分配 |
| 3 | VEQ (2026) | 明确提出 `modality-expert-aware` 的压缩视角 | 它是量化误差建模，不是 merge ratio 分配 |
| 4 | Quant Experts (2026) | 也利用 token-dependent 差异来指导 VLM 压缩补偿 | 它聚焦重要通道与量化误差重建，没有把 token 冗余映射到 merge 决策 |
| 5 | Improving MoE Compute Efficiency by Composing Weight and Data Sparsity (2026) | 公开结果说明视觉 token 会更激进地被送去 null experts，支持模态预算非对称 | 它研究的是训练/推理稀疏性组合，不是 post-training expert merging |

## 3. 机制级差异

### 与 token pruning 线的差异

1. `QMoP / POP` 的核心对象是 token、投影器或通道。
2. `ViTAL-Merge` 把这些信息当作 `merge budget signal`，最终动作仍然发生在 expert merge 上。
3. 也就是说，这条线不是和 token pruning 竞争，而是把 token 冗余变成 merge 决策依据。

### 与 quantization 线的差异

1. `VEQ / Quant Experts` 已经说明不同模态、不同 token 的重要性不同。
2. `ViTAL-Merge` 往前再走一步：既然重要性不同，为什么 merge ratio 还要统一？
3. 新点不在“发现差异”，而在“把差异显式写成压缩预算分配器”。

### 与传统 merge 线的差异

1. 传统 merge 主要优化“如何合并”。
2. `ViTAL-Merge` 主要优化“哪里该多合并，哪里该少合并”。
3. 在多模态里，这个问题本身可能比 merge 算子更关键。

## 4. Novelty 风险评级

**评级：中偏高**

### 降低风险的因素

- 当前公开工作里，确实有很多论文承认多模态压缩预算不对称，但很少把它写成 expert merge 论文的主角。
- 这条线很好地结合了多模态特有问题与“提速”目标。

### 提高风险的因素

- 审稿人可能会说：这更像 compression policy，而不是一个足够深的 merging 机制贡献。
- 如果没有明确展示“统一预算真的错”，而只是报告一些经验调参结果，论文力度会不足。

## 5. 投稿叙事建议

### 推荐叙事

- 把故事讲成：**现有 multimodal compression 都承认视觉与语言冗余不对称，但 expert merging 仍沿用统一 merge ratio；我们提出首个由模态冗余驱动的 merge budget allocator。**

### 论文标题风格建议

- `ViTAL-Merge: Visual-Token-Aware Budget Allocation for Multimodal Expert Merging`
- `Uneven Redundancy, Uneven Merging: Modality-Skew-Aware Compression for Multimodal MoEs`

### 最佳卖点

- “多模态里不是 merge 不够聪明，而是 merge 预算分错了地方。”

## 6. 结论

- 这条 idea 没有明显同题撞车，但与 token pruning / VLM compression 文献较近。
- 它适合作为高质量备选，因为落地感和效率叙事都很强。
- 若作为主线，必须把“budget allocation 本身为何是新问题”讲得足够硬，否则容易被认为只是一个策略增强。
