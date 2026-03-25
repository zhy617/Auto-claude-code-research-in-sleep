# IDEA_REPORT

日期：2026-03-24  
输入基线：`IDEA_LANDSCAPE.md`

## 0. 排序规则

- 新颖性：40%
- 可落地性：30%
- 论文价值：30%
- 总分公式：`0.4 * 新颖性 + 0.3 * 可落地性 + 0.3 * 论文价值`
- 评分为 `1-10`

## 1. 10 个候选 idea 总表

| 排名 | Idea 名称 | 核心一句话 | 新颖性 | 可落地性 | 论文价值 | 总分 |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | BridgePreserve | 先识别 `visual / language / bridge / shared` 角色，只在同角色内 merge，并显式保护 bridge experts | 8.9 | 7.6 | 8.7 | 8.46 |
| 2 | ViTAL-Merge | 按视觉 token 冗余、桥接重要性与语言稀缺性分配非均匀 merge 预算 | 8.3 | 8.0 | 8.2 | 8.17 |
| 3 | RouteBridge | merge 后用模态条件信号重写 router latent space，恢复跨模态桥接路由 | 7.9 | 8.1 | 8.0 | 7.99 |
| 4 | BridgeDistill | 不直接保护 bridge expert，而是蒸馏其跨模态桥接功能到少量共享专家 | 8.1 | 6.6 | 8.0 | 7.60 |
| 5 | ExpertRoleProbe | 用 paired image-text calibration 数据自动发现 expert role，再驱动后续 merge | 7.8 | 7.0 | 7.8 | 7.56 |
| 6 | Merge-or-Prune-by-Role | 按角色决定是 merge 还是 prune，而不是统一策略 | 7.4 | 7.4 | 7.5 | 7.43 |
| 7 | NullExpertMerge | 为视觉侧引入 null experts / skip slots，再将冗余视觉专家合并到跳过路径 | 7.8 | 6.8 | 7.4 | 7.36 |
| 8 | Cross-Encoder Consolidation | 把多视觉 encoder 视作“异种专家”做统一压缩 | 8.4 | 5.5 | 7.8 | 7.33 |
| 9 | OT-Bridge Matching | 用最优传输在跨模态专家间寻找“桥接对齐”后再 merge | 8.2 | 5.3 | 7.6 | 7.15 |
| 10 | Multimodal Logit-LSE Port | 把你文本线里的 router/logit 聚合直接移植到 VLM merge | 5.6 | 8.6 | 6.0 | 6.55 |

## 2. Top-3 详细展开

### Idea 1. BridgePreserve

**全名**：`BridgePreserve: Role-Aware Bridge-Preserving Expert Merging for Multimodal MoE`

**核心假设**

- 多模态 MoE 的退化不主要来自“平均化不够精细”，而主要来自“把不同角色的专家误合并了”。
- 其中最应保护的是承担跨模态对齐与信息转译的 bridge experts。

**方法直觉**

- 第一步，不直接按参数或激活相似度聚类，而是先给专家打上粗角色标签：`visual-only / language-only / bridge / shared`。
- 第二步，只允许同角色内 merge；对 bridge experts 设置更严格阈值，甚至默认不 merge。
- 第三步，merge 后 router 不追求恢复原始全局分布，而是优先恢复“桥接路径是否还能被选中”。

**与最近工作的差异**

- 不同于 `MergeMoE / HC-SMoE / M-SMoE`：它们默认专家可在同类空间比较，你这里先解决“哪些专家本来就不该比”。
- 不同于 `Router KD / DenseMixer`：它们主要修路由，你的主问题定义是“先别把桥拆了”。
- 不同于 `VEQ / Quant Experts`：它们处理多模态异质性，但对象是量化；你这里把异质性上升为 merge 的一等约束。

**潜在审稿风险**

- 最大风险是“role-aware”容易被质疑成 heuristic，如果角色识别靠太多人为规则，论文会显得工程拼接。
- 第二个风险是“bridge expert”概念如果没有可操作定义，会被问：你保护的到底是什么？

**最小纸面验证路径**

- 用角色标注前后的 merge 结果比较：`same-role merge` vs `role-agnostic merge`。
- 重点报告三类指标：`跨模态任务保持率`、`bridge 路由占比变化`、`merge 后 expert output reconstruction error`。
- 纸面核心图应该是：相同压缩率下，role-aware merge 比普通 merge 更稳，且损失主要少在桥接相关任务。

### Idea 2. ViTAL-Merge

**全名**：`ViTAL-Merge: Visual-Token-Aware Adaptive Merge Budgeting for Multimodal MoE`

**核心假设**

- 多模态压缩预算不该均匀分配，因为视觉 token 冗余显著高于文本 token，但跨模态桥接路径反而更脆弱。
- 因此真正高效的 merge 应该是“视觉侧更敢压、语言侧谨慎压、桥接侧少压或不压”。

**方法直觉**

- 先估计每层的模态结构特征：视觉 token 占比、visual-token 冗余度、bridge activation 强度。
- 再把 merge budget 从“固定每层 merge ratio”改成“按模态与角色动态分配”。
- 本质上不是发明新的 merge 算子，而是发明新的 `budget allocator`。

**与最近工作的差异**

- 不同于 `QMoP / POP`：它们主要压 token 或结构剪枝；你这里仍然围绕 expert merge，但预算被 token 冗余信息驱动。
- 不同于 `VEQ / Quant Experts`：它们做的是量化误差分配；你这里做的是 merge 风险分配。
- 不同于 `FastMMoE`：你不是把 pruning 单独做好，而是把 token 冗余信号直接转成 expert merge 决策。

**潜在审稿风险**

- 容易被审稿人认为像“好的 compression policy”而不是“新的 multimodal MoE merging 问题”。
- 如果没有清楚解释为什么预算分配本身比新 merge 算子更重要，卖点会显得偏弱。

**最小纸面验证路径**

- 设定固定总压缩率，比较 `uniform merge budget` 与 `modality-skew-aware budget`。
- 纸面上最容易 defend 的指标是：在相同总压缩率下，后者在视觉问答和跨模态推理任务掉点更少，同时 latency 改善不差。
- 再补一张图：merge budget 如何随着视觉 token 冗余度变化。

### Idea 3. RouteBridge

**全名**：`RouteBridge: Modality-Conditioned Router Repair after Multimodal Expert Merging`

**核心假设**

- 多模态 merge 后最先坏掉的，不一定是 expert 本体，而是 router 的判别几何。
- 文本 MoE 中常见的频率补偿、平均权重、logit 调整，在多模态下会被模态偏置放大。

**方法直觉**

- merge 完成后，不直接在原始 token 表征上做 router 修补，而是先抽一个轻量的模态条件摘要。
- 让 router 在一个更低维、更模态感知的 latent routing space 中重新判别。
- 对 bridge experts 单独加一条“不要被视觉大流量冲掉”的保护项。

**与最近工作的差异**

- 不同于 `Router KD`：不是单纯蒸馏原路由输出，而是改路由空间本身。
- 不同于 `SAMoE-VLA / CoVFT`：你不是训练原生多模态 router，而是研究 `after merging` 的恢复问题。
- 不同于你已有文本线：这里新东西不在 logit 聚合，而在多模态条件化的 router 几何。

**潜在审稿风险**

- 路由论文很多，容易被质疑“只是又一个 router calibration 方法”。
- 如果与 merge 的关系不够强，可能被审稿人要求单独作为 router paper 来看，从而削弱主线。

**最小纸面验证路径**

- 固定相同 merge 策略，比较 `no repair`、`freq/logit repair`、`modality-conditioned repair`。
- 关键图应展示：普通 repair 会被视觉高频 token 主导，而你方法能保住 bridge 路径。
- 最重要的不是全局 accuracy，而是 merge 后跨模态 routing consistency 与 bridge utilization。

## 3. 淘汰的 7 个 idea 及原因

| Idea | 淘汰理由 |
| --- | --- |
| BridgeDistill | 虽然论文感强，但已经偏向“蒸馏桥接能力”，离“专家合并提速”主线稍远。 |
| ExpertRoleProbe | 更像上游分析工具，单独成文不够完整，适合作为 BridgePreserve 的子模块。 |
| Merge-or-Prune-by-Role | 很合理，但作为题目本身稍散，容易变成决策框架论文而不是核心机制论文。 |
| NullExpertMerge | 和最新 null/skip sparsity 线太接近，且实现假设多，选题风险偏高。 |
| Cross-Encoder Consolidation | 新颖但范围过大，已经从“expert merging”跨到“多编码器统一压缩”，两周纯想法阶段不适合。 |
| OT-Bridge Matching | 数学上好看，但落地性弱，容易陷入“相似度定义过重”的泥潭。 |
| Multimodal Logit-LSE Port | 最容易做，但也最像把文本方法直接迁移到多模态，低新颖度风险明显。 |

## 4. 当前排序结论

- 最推荐主线：`BridgePreserve`
- 最稳健备选：`ViTAL-Merge`
- 如果你想最大化延续现有 router 经验：`RouteBridge` 可以作为第三条保底线，但不建议当第一主线

## 5. 为什么 Top-1 不是“最容易做”的那条

- `BridgePreserve` 的优势不在最容易，而在它最准确抓住了“多模态 merge 为什么和文本 merge 不一样”。
- 它既保留了你的原主线 `expert merging for faster inference`，又避免了低新颖度的直接迁移。
- 从审稿叙事上，它也最容易回答一句关键问题：**为什么这个问题只有在多模态里才成立？**
