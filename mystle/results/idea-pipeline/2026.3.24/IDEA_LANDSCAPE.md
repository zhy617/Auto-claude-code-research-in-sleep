# IDEA_LANDSCAPE

日期：2026-03-24  
主题：多模态 MoE 专家合并的新挑战

## 0. 使用说明

- `文献已验证`：来自公开 arXiv/OpenReview 检索结果，或你 Obsidian 中已有的论文笔记。
- `推断`：基于已有文献现象做出的研究判断，尚未看到论文直接验证。
- 本文只做选题与方向判断，不包含任何实验执行。

## 1. 代表工作表

### 1.1 纯文本/通用 MoE 合并、压缩、路由修复

| 工作 | 年份 | 方向 | 对本题的启发 | 证据状态 |
| --- | --- | --- | --- | --- |
| MC-SMoE / M-SMoE (`Merge, Then Compress`) | 2024 | 聚类后专家合并 | 证明“相似专家可合并”在文本 MoE 中成立，但默认专家是同质可比较的 | 文献已验证 |
| HC-SMoE (`Retraining-Free Merging of Sparse MoE via Hierarchical Clustering`) | 2024-2025 | 基于激活相似度的层次聚类合并 | 文本场景里常用“输出相似度”做代理；多模态里该代理可能跨模态失真 | 文献已验证 |
| MergeME (`Model merging techniques for homogeneous and heterogeneous MoEs`) | 2025 | 同构/异构 MoE 合并 | 强调专家语义与数据含义需要先被识别，不能只看 loss | 文献已验证 |
| DenseMixer (`Improving MoE post-training with precise router gradient`) | 2025 | 后训练路由优化 | 说明 router 不是附属问题，而是压缩/合并后的核心恢复对象 | 文献已验证 |
| Drop or Merge? | 2026 | 自适应选择 prune 或 merge | 暗示“不是所有冗余都适合合并”；多模态更可能需要按角色分配策略 | 文献已验证 |
| MergeMoE (`Expert Output Merging`) | 2025/2026 | 输出空间建模的专家合并 | 把参数合并改写为输出逼近；这在多模态里更自然，因为参数空间可比性更差 | 文献已验证 |
| REAP the Experts | 2025/2026 | generative 任务中 pruning 优于 merging | 指出合并会损失细粒度路由自由度；对 VLM/MLLM 这种跨模态生成任务更危险 | 文献已验证 |
| PuzzleMoE | 2025 | 稀疏专家合并 + bit-packed 推理 | 说明“保留共享参数 + 保留少量专有参数”比纯平均更稳 | 文献已验证 |
| LightMoE | 2026 | expert replacing | 提供第三条路线：不是 merge 或 prune，而是把冗余专家替换成轻量模块 | 文献已验证 |
| Is Retraining-Free Enough? (`Router KD`) | 2026 | 仅校准 router 的压缩恢复 | 明确指出压缩退化的重要来源是 router-expert mismatch | 文献已验证 |
| SERE | 2026 | 相似专家重路由加速 | 动态 reroute 而非静态 merge，说明“路由层重写”本身就是一条效率线 | 文献已验证 |
| FusionRoute | 2026 | token 级协作路由 + logit 补偿 | 说明 router 可以承担“补全被压缩能力”的角色，而不只是选专家 | 文献已验证 |

### 1.2 多模态 MoE / VLM / MLLM 相关工作

| 工作 | 年份 | 方向 | 对本题的启发 | 证据状态 |
| --- | --- | --- | --- | --- |
| LIMoE | 2022-2023 | 多模态中自然形成专家专门化 | 支持“模态/功能专门化会自然涌现”，因此跨角色直接合并可能有害 | 推断 |
| MoME | 2023-2024 | 分离 visual experts 与 language experts | 直接告诉我们：视觉专家和语言专家不应被当作同类对象处理 | 推断 |
| Uni-MoE | 2023-2024 | 前端分模态，后端统一稀疏主干 | 暗示后端存在“共享专家”与“桥接专家”角色 | 推断 |
| MoE-LLaVA | 2024 | VLM 中引入 MoE | 证明 MoE 已进入 VLM 主干，压缩问题会从文本迁移到 VLM | 推断 |
| DynMoE-LLaVA | 2024-2025 | 动态专家选择 | 多模态路由更依赖输入结构，静态频率统计可能不足 | 推断 |
| FastMMoE | 2024-2025 | visual token pruning + expert pruning | 视觉 token 冗余更高，压缩预算不应在模态间平均分配 | 推断 |
| CoVFT | 2026 | 多模态上下文感知视觉微调 + CoMoE | 公开结果表明视觉偏好冲突真实存在，多模态优化信号本身并不稳定 | 文献已验证 |
| SAMoE-VLA | 2026 | scene-level routing 替代 token-level routing | 公开结果显示将文本 MoE 的 token routing 直接搬到 VLA 会导致不稳定和安全退化 | 文献已验证 |
| QMoP | 2026 | Mixture-of-Projector 做视觉 token 压缩 | 压缩早已从“只压专家”转向“压视觉 token/投影器/融合链路” | 文献已验证 |
| POP | 2026 | 在线结构化剪枝，覆盖 LLM/MoE/VLM | 说明 VLM 压缩需要上下文相关的动态结构决策，而非固定静态压缩 | 文献已验证 |
| Quant Experts | 2026 | token-aware VLM 量化补偿 | 公开结果强调重要通道在模态间、token 间分布差异很大 | 文献已验证 |
| VEQ | 2026 | modality-aware quantization for MoE VLMs | 公开结果直接点出两类异质性：`modality discrepancy` 与 `expert heterogeneity` | 文献已验证 |
| Improving MoE Compute Efficiency by Composing Weight and Data Sparsity | 2026 | 组合权重稀疏与数据稀疏 | 在 VLM 训练中，模型会更激进地把视觉 token 路由到 null experts，说明模态预算天然不对称 | 文献已验证 |
| Beyond Language Modeling: An Exploration of Multimodal Pretraining | 2026 | 原生多模态预训练中的 MoE | 公开结果指出 MoE 能自然吸收 vision/text 的 scaling asymmetry，并诱导模态专门化 | 文献已验证 |

## 2. 纯文本合并 vs 多模态合并：关键差异

| 维度 | 纯文本 MoE 合并 | 多模态 MoE 合并 | 结论 |
| --- | --- | --- | --- |
| Expert specialization | 多为语义域、任务域或 token pattern 专精 | 还叠加了模态域、跨模态桥接、视觉细粒度感知等角色 | 多模态专家不是单一“同类专家” |
| Router behavior | token-level 语义路由为主 | 受模态类型、视觉密度、跨模态对齐阶段、场景上下文共同影响 | 文本 router 代理信号不再可靠 |
| Compression budget | 常默认所有专家预算大致对等 | 视觉 token 更冗余，但 bridge experts 更脆弱 | 压缩预算必须按角色/模态非均匀分配 |
| Similarity metric | 激活相似度、路由相似度、参数相似度常可用 | 跨模态表示空间往往不可比，视觉与语言专家的“相似”可能是伪相似 | 需要 role-aware / bridge-aware 相似度 |
| Failure mode | routing collapse、平均化、长尾专家饿死 | 还会出现跨模态对齐断裂、桥接路径损坏、视觉过压缩导致语言侧补偿失败 | 多模态失败更偏结构性而非单点退化 |
| Evaluation | PPL、下游 accuracy、load balance 常足够 | 还需关注跨模态一致性、桥接稳定性、token 保真与场景鲁棒性 | 文本指标不足以判断多模态 merge 是否成功 |

## 3. Challenge Taxonomy

### C1. Expert Role Heterogeneity

- `文献已验证`：MoME/Uni-MoE/SAMoE-VLA/VEQ/多模态预训练工作都指向同一个事实，多模态系统里的专家承担不同角色，而非简单同质专家池。
- `推断`：如果不先识别 `visual-only / language-only / bridge / shared` 等角色，任何聚类合并都容易把“不同功能的专家”误合并。

### C2. Router Signal Mismatch

- `文献已验证`：Router KD、DenseMixer、SAMoE-VLA 都显示路由器在结构改变后会严重失配。
- `推断`：多模态 merge 后，router 不只是“选错专家”，更可能失去对跨模态桥接链路的辨识能力。

### C3. Modality-Imbalanced Redundancy

- `文献已验证`：QMoP、POP、VEQ、Quant Experts、组合权重与数据稀疏工作都表明视觉 token/视觉通道冗余更高，且重要性随 token 改变。
- `推断`：多模态压缩不应统一使用文本 MoE 中的层内固定 merge ratio，而应采用模态不对称预算。

### C4. Cross-Modal Alignment Fragility

- `文献已验证`：CoVFT 指出视觉偏好冲突会破坏多模态稳定性；SAMoE-VLA 指出错误 routing 粒度会带来行为退化。
- `推断`：某些专家不是为了单模态性能，而是为了维持视觉到语言的“桥”；这些专家一旦合并，性能下降会体现在复杂跨模态任务上而不是简单 benchmark 上。

### C5. Multi-Encoder / Multi-Pathway Non-Comparability

- `文献已验证`：多模态笔记中已整理出多编码器、多路径结构是常态；QMoP 等工作也表明压缩对象不再局限于单一 FFN expert。
- `推断`：来自不同编码器、不同 pathway 的专家不满足文本 MoE 中默认的可比性假设，直接做参数或激活相似度聚类风险很高。

### C6. Metric Mismatch

- `文献已验证`：你现有笔记已总结出 ECE、Oracle Routing Gap、Reconstruction Error 比 entropy/CV 更可信。
- `推断`：在多模态中，仅看 entropy/CV 甚至 PPL 可能错过“桥接已断但文本侧仍看起来正常”的失败。

### C7. Compensation Scope Mismatch

- `文献已验证`：FusionRoute、Router KD 说明补偿可以在 router/logit 层发生，不一定非得回头调 expert 本体。
- `推断`：多模态场景中，最佳补偿目标很可能不是“恢复所有专家”，而是优先恢复 bridge expert 与关键视觉路径。

## 4. 现有 merge/prune/distill/compress 方法在多模态中的典型失效点

1. `同质专家假设失效`：文本方法默认专家可在同一空间比较；多模态专家经常承担不同模态或桥接功能。
2. `频率统计失真`：视觉 token 数量远大于文本 token，激活频率会天然偏向视觉侧，进而误导 merge 权重。
3. `路由代理不稳`：token-level router logits 在多模态里可能更多反映模态密度而非真实功能相似性。
4. `跨模态桥被抹平`：桥接专家被平均化后，文本侧 loss 不一定立刻崩，但复杂视觉问答/推理会显著掉点。
5. `统一压缩率错误`：对所有层、所有模态、所有专家使用同一压缩率，忽略了视觉冗余高但桥接路径脆弱的结构事实。
6. `恢复目标选错`：只校准 router 或只补偿 expert 都可能不够，多模态里需要显式恢复“谁负责跨模态交互”。

## 5. 三个最值得做的问题定义

### P1. Role-Aware Bridge-Preserving Multimodal Expert Merging

- 一句话：先识别专家角色，再只在同角色内合并，并显式保护 bridge experts。
- 为什么值得做：它直接回应多模态与文本 MoE 的本质差异，而且仍然延续“通过 expert merging 提升推理效率”的主线。
- `文献已验证`：异质专家、router mismatch、alignment fragility 都有公开支持。
- `推断`：只要 role discovery 足够稳，这条线比“直接把文本 merge 搬到 VLM”更有新颖性。

### P2. Modality-Skew-Aware Merge Budget Allocation

- 一句话：根据视觉/语言 token 冗余差异与桥接重要性，非均匀分配 merge/prune 预算。
- 为什么值得做：它把多模态特有的 `modality imbalance` 直接转化为压缩决策，而不是只把它当背景事实。
- `文献已验证`：QMoP、POP、VEQ、Quant Experts、weight+data sparsity 都支持“模态间冗余不对称”。
- `推断`：这条线容易和 token pruning 形成协同，是一条更偏系统效率的论文主线。

### P3. Modality-Conditioned Router Repair After Merging

- 一句话：在 merge 完成后，用低成本的模态条件信号重写路由空间，而非只做频率或 logit 平均。
- 为什么值得做：你已有文本 MoE 经验正好能迁移到 router 问题，但新贡献来自“多模态条件化”。
- `文献已验证`：Router KD、DenseMixer、SAMoE-VLA、CoVFT 都支持路由修复的重要性。
- `推断`：这条线论文叙事清晰，但若只做 router 层，可能被审稿人认为不如 role-aware merging 根本。

## 6. 当前阶段结论

- 结论 1：多模态专家合并最核心的新挑战不是“怎么更稳地平均参数”，而是“怎么处理专家角色异质性与跨模态桥接”。
- 结论 2：最危险的低新颖度路线是“把文本 MoE 的 frequency-weighted merge / SVD merge 直接搬到 VLM”。
- 结论 3：最值得推进的主线，不是再发明一个通用 merge 算子，而是把 `role-aware`、`bridge-aware`、`modality-skew-aware` 三件事组合成新的问题定义。
