# FINAL_IDEA_SELECTION

日期：2026-03-24

## 1. Reviewer-style 最终结论

### 推荐主线 idea

**BridgePreserve**  
`Role-Aware Bridge-Preserving Expert Merging for Multimodal MoE`

### 备选 idea

**ViTAL-Merge**  
`Visual-Token-Aware Adaptive Merge Budgeting for Multimodal MoE`

## 2. 为什么主线是 BridgePreserve

从审稿视角看，`BridgePreserve` 最强的地方不是它最容易做，而是它最准确地抓住了“多模态 expert merge 相比文本 merge 的新增矛盾”。  
它回答的是一个真正的新问题：**在多模态里，专家不再默认同质，某些专家承担的是跨模态桥接功能，因此 merge 的第一步不该是算谁像谁，而该先判断谁根本不该被放进同一个 merge 池。**

这比“换一个更好的 merge 权重”更有问题定义价值，也比“只做 router 修补”更靠近你原本的主线。

## 3. 每个方案的 killer weakness

### 主线：BridgePreserve

- **killer weakness**：如果 `bridge expert` 与 `role-aware` 不能被明确定义并稳定识别，整条路线就会被批评为 heuristic 拼装，理论硬度不足。

### 备选：ViTAL-Merge

- **killer weakness**：很容易被审稿人降格理解为“一个更好的 compression policy”，而不是一个足够深的 multimodal expert merging 新机制。

## 4. 最小可 defend 的核心 claim

### 主线 claim

**在多模态 MoE 中，merge 失败的关键不是平均公式不够好，而是忽略了专家角色异质性；只在同角色内 merge 并保护 bridge experts，可在相同压缩率下显著降低跨模态能力退化。**

### 备选 claim

**由于视觉与语言 token 冗余度天然不对称，多模态 expert merging 应采用模态不均匀预算分配；在相同总压缩率下，这种预算分配比统一 merge ratio 更稳。**

## 5. 论文卖点摘要（150-200字）

本文关注一个此前未被系统化提出的问题：文本 MoE 中默认成立的“专家同质可比较”假设，在多模态模型里会失效。视觉专家、语言专家以及承担跨模态对齐的桥接专家在功能上并不等价，因而直接沿用文本场景中的聚类合并、频率加权或统一压缩率，往往会带来跨模态能力断裂而不仅是一般性精度下降。基于这一观察，我们提出 role-aware 的多模态 expert merging 视角：先识别专家角色，只在同角色内合并，并显式保护 bridge experts。该问题定义既延续了“通过 expert merging 提升推理效率”的主线，又准确体现了多模态特有的新挑战，为 VLM/MLLM 下的 MoE 压缩提供了更有新颖性的研究切入口。

## 6. 接下来两周的“纯想法阶段”工作清单

1. 把 `bridge expert` 的操作化定义写硬，至少给出 2-3 种候选定义方式，并比较优缺点。
2. 把 `role-aware` 的最小框架图画出来，明确哪些步骤是必要模块，哪些只是可选增强。
3. 为主线整理一页“为什么文本 merge 假设在多模态失效”的论证图，作为未来论文 teaser。
4. 把 `BridgePreserve` 与 `MergeMoE / Router KD / VEQ / SAMoE-VLA` 的关系画成定位图，避免贡献边界模糊。
5. 设计最小实验叙事模板，只定义任务、指标、对比项，不执行实验。
6. 准备一个“如果 role-aware 过重怎么办”的降级版本，即退化到 `ViTAL-Merge` 作为备选路线。
7. 为主线准备 3 个审稿人最可能问的问题，并先写出回应草案。

## 7. 最终一句话建议

如果你只押一条线，就押 `BridgePreserve`；它最像一个真正属于“多模态 MoE 专家合并”的问题，而不是文本方法的换皮延伸。
