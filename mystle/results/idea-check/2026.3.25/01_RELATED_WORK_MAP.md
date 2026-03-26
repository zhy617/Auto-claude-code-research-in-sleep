# 01 RELATED WORK MAP

日期：2026-03-25  
主题：`BridgePreserve` 的 related-work mapping

## 0. 检索范围与证据等级

- `本地材料`：`NOVELTY_IDEA_1.md`、`IDEA_LANDSCAPE.md`、`IDEA_REPORT.md`、Obsidian 笔记 `Multimodal MoE Merging.md`、`Merging and Adapting.md`。
- `外部检索`：以 2024-2026 为主，补充必要奠基线；重点核对了 `MergeMoE`、`VEQ`、`Quant Experts`、`Router KD`、`SAMoE-VLA`、`REAP`、`PuzzleMoE`、`FastMMoE`。
- `文献明确支持`：来自论文摘要页、HTML 正文片段或你已有笔记中明确记录的内容。
- `我的推断`：基于这些文献做的路线归纳与对 BridgePreserve 的定位判断。

## 1. 一句话结论

- 当前最接近 `BridgePreserve` 的不是单一论文，而是三条线的交叉：
  1. `MergeMoE / HC-SMoE / PuzzleMoE` 的 expert merge 线
  2. `Router KD / DenseMixer / SAMoE-VLA` 的 routing mismatch / routing redesign 线
  3. `VEQ / Quant Experts / FastMMoE` 的 multimodal heterogeneity / modality imbalance 线
- `我的推断`：真正的空位在于，还没人把“多模态异质性”系统改写成 `expert merging constraint`，并把 `bridge path` 当成被保护对象。

## 2. Taxonomy / Grouping

### G1. 通用 MoE expert merge / compress

这条线研究“怎么压专家”，默认前提通常是 experts 至少部分可比较。

| 论文 | 年份 | 核心路线 | 与本题关系 |
| --- | --- | --- | --- |
| M-SMoE / MC-SMoE | 2024 | 基于路由或聚类的 expert merging | 奠基工作，默认专家可聚合 |
| HC-SMoE | 2024-2025 | 基于激活/输出相似度的层次聚类合并 | 提供常见相似度代理，但未考虑多模态角色 |
| MergeMoE | 2025 | 从输出空间解释 expert merging | 最近、最危险的 merge 主线 |
| PuzzleMoE | 2025 | 稀疏 expert merging + bit-packed inference | 说明“共享 + 专有”比纯平均更稳 |
| REAP the Experts | 2025/2026 | 认为生成任务里 pruning 常优于 merging | 对你的工作形成最强反对意见 |
| DM-MoE (`Drop or Merge?`) | 2026 | 自适应决定 prune 还是 merge | 暗示不同层/专家应使用不同压缩策略 |
| LightMoE | 2026 | expert replacing 而非 merge/prune | 说明压缩范式正在扩展 |

### G2. 压缩后 router 修复 / routing geometry

这条线研究结构改变后，为什么 router 会失配，以及该怎么补。

| 论文 | 年份 | 核心路线 | 与本题关系 |
| --- | --- | --- | --- |
| DenseMixer | 2025 | 更精确的 router gradient | 证明 router 不是附属组件 |
| Router KD (`Is Retraining-Free Enough?`) | 2026 | 仅更新 router 的轻量校准 | 会被用来质疑你是否只是在“先 merge 再修 router” |
| FusionRoute | 2026 | 让 router 额外承担补偿输出 | 表明补偿对象可以是路由层而不是 expert 本体 |
| SAMoE-VLA | 2026 | 从 token-level routing 改为 scene-level routing | 提供多模态 routing 信号失配的直接证据 |

### G3. 多模态 MoE / multimodal heterogeneity

这条线不一定做 merge，但明确承认多模态 experts 或 token 不是同质对象。

| 论文 | 年份 | 核心路线 | 与本题关系 |
| --- | --- | --- | --- |
| LIMoE | 2022/2023 | 证明多模态专家专门化可自然出现 | 奠基背景 |
| MoME | 2023/2024 | 视觉 experts 与语言 experts 分离 | 最直接支持“专家角色不同” |
| Uni-MoE | 2023/2024 | 前端分模态、后端统一稀疏主干 | 暗示共享/桥接角色存在 |
| MoE-LLaVA / DynMoE-LLaVA | 2024-2025 | 把 MoE 引入 VLM / 动态专家选择 | 提供真实模型载体 |
| SAMoE-VLA | 2026 | scene-adaptive routing | 说明 token routing 不一定适合多模态 |
| Beyond Language Modeling | 2026 | 原生多模态预训练中的 MoE scaling | 支持模态专门化与不对称性 |

### G4. 多模态压缩 / 量化 / pruning

这条线不是 merge，但与你最容易形成“设定借鉴 + reviewer 比较”。

| 论文 | 年份 | 核心路线 | 与本题关系 |
| --- | --- | --- | --- |
| FastMMoE | 2025 | 动态 expert activation + routing-aware token pruning | 提供最可复用的 multimodal efficiency 实验框架 |
| QMoP | 2026 | Mixture-of-Projector 做视觉 token 压缩 | 邻近但不直接竞争 |
| POP | 2026 | 在线结构化剪枝 | 强调动态结构决策 |
| VEQ | 2026 | modality-aware quantization for MoE VLMs | 最直接证明模态差异 + expert heterogeneity |
| Quant Experts | 2026 | token-aware adaptive error compensation | 证明 token-level / modality-level 差异不可忽略 |

## 3. 相关论文总表

### 3.1 直接相关

这些论文最可能被 reviewer 直接拿来与你比较。

| 论文 | 问题 | 方法 | 核心机制 | 主要局限 | 与 `BridgePreserve` 的关系 |
| --- | --- | --- | --- | --- | --- |
| MergeMoE | 如何更优地压缩 MoE experts | 从 expert output 而非参数平均解释合并 | 通过优化构造 compression matrices，使 merged outputs 逼近原 outputs | 默认被合并 experts 至少在同一输出比较空间中可近似 | `最接近主竞争者`；你需要解释为什么多模态里“先决定可比性”比“更优 output merge”更先验 |
| HC-SMoE | 如何基于激活相似度做 retraining-free merge | 层次聚类 + 路由权重求和 | 用激活/输出相似度做聚类代理 | 相似度假设在跨模态空间可能失真 | 提供一个很自然的 `role-agnostic merge` baseline |
| PuzzleMoE | 如何在压缩时保留共享与专有知识 | 稀疏 expert merging + 双掩码 | 把 shared / private 参数拆开编码 | 仍未处理多模态角色识别 | 可作为“不是纯平均、而是部分保留”的强 baseline |
| REAP the Experts | 生成任务中 merge 是否真的优于 prune | 理论分析 + router-weighted expert pruning | 认为 merge 会带来功能子空间坍塌 | 结论主要来自 generative SMoE，不是多模态 bridge setting | 它是你最强反对意见来源：审稿人会问“为什么不直接 prune bridge 以外的 experts？” |
| Router KD | 压缩后退化是否主要来自 router mismatch | 仅更新 router 的 KD 校准 | 在不动 experts 的前提下恢复 next-token distribution | 不回答“哪些 experts 不该被改动” | 你必须证明 bridge protection 不等价于 router calibration |

### 3.2 相邻但可借鉴

这些论文不直接做 expert merge，但给你最重要的结构证据与实验设置。

| 论文 | 问题 | 方法 | 核心机制 | 主要局限 | 与 `BridgePreserve` 的关系 |
| --- | --- | --- | --- | --- | --- |
| VEQ | 如何量化 MoE VLM 而不破坏性能 | dual-aware quantization | 同时建模 modality discrepancy 与 expert heterogeneity | 对象是 quantization，不是 merge | `最重要的旁证`：公开论文已经承认多模态 MoE 里 experts 不均等、模态不对称 |
| Quant Experts | 如何做 token-aware VLM quantization compensation | token-aware adaptive error reconstruction | 区分 token-independent / token-dependent channels | 聚焦量化误差补偿 | 支持“重要性随 token 与模态变化” |
| FastMMoE | 如何在 MoE-MLLM 中 training-free 加速 | expert activation reduction + routing-aware token pruning | 用 routing distribution 判断视觉 token 冗余，并只更激进压视觉侧 | 不是 expert merge | 给你最直接的 `多模态效率 + 路由分析` 实验模板 |
| SAMoE-VLA | 为什么 LLM 里的 token-level MoE routing 迁移到 VLA 会失效 | 以 scene representation 驱动 routing | 用 BEV scene context 代替 token embedding 产生路由信号 | 设置偏自动驾驶 VLA | 证明“多模态 routing 信号失配”是真问题，而不是你主观担心 |
| DenseMixer | 如何在后训练中改进 router 学习 | 更精确的 router gradient | 训练时利用 inactive experts 获得更准梯度 | 不是压缩论文 | 说明 router 几何本身值得分析，但与你不同在于它不讨论 merge 约束 |

## 4. 最可能形成对比的 4 条主线

### 主线 A：`更好的 merge 算子`

- 代表：`HC-SMoE`、`MergeMoE`、`PuzzleMoE`
- 他们的共同立场：先接受 experts 可以被比较，再去优化“怎么合并更好”。
- 你需要反驳的点：在多模态里，`先比较谁能比` 比 `怎么比` 更根本。

### 主线 B：`压缩后 router 修复`

- 代表：`Router KD`、`DenseMixer`、`FusionRoute`
- 他们的共同立场：压缩不可避免，关键在于事后恢复 router 或输出。
- 你需要反驳的点：如果桥接路径被错误合并，router repair 只是事后补锅，未必能恢复被抹平的角色结构。

### 主线 C：`多模态异质性已被承认，但用于量化/剪枝`

- 代表：`VEQ`、`Quant Experts`、`FastMMoE`
- 他们的共同立场：vision/text token 与 experts 的重要性不对称，统一压缩不合理。
- 你要站的位置：不是再次证明异质性存在，而是把它变成 `merge admissibility constraint`。

### 主线 D：`多模态 routing 该改信号而不是照搬文本 MoE`

- 代表：`SAMoE-VLA`
- 他们的共同立场：token-level routing 信号在多模态/VLA 里可能错位。
- 你要借力的点：如果 routing 本身不稳定，那么基于 naive routing/activation similarity 做跨角色 merge 的风险更高。

## 5. 直接竞争论文 shortlist

### Top-5 shortlist

| 排名 | 论文 | 危险点 | 为什么危险 |
| --- | --- | --- | --- |
| 1 | MergeMoE | 会被说“你只是先分组再用更好的 merge” | 因为它已经把 merge 提升到输出空间层面，审稿人会认为这是更根本的方法线 |
| 2 | Router KD | 会被说“你观察到的退化只是 router-expert mismatch” | 因为它用极轻量校准就能恢复一部分性能 |
| 3 | VEQ | 会被说“多模态异质性早就有人讲过了” | 因为它明确写出了 modality discrepancy 和 expert heterogeneity |
| 4 | FastMMoE | 会被说“真正有效的是模态不对称压缩，不一定需要 merge” | 因为它直接给出多模态效率收益和 routing-aware 证据 |
| 5 | SAMoE-VLA | 会被说“问题在 routing signal，不在 merge 本身” | 因为它证明 token-level routing 迁移会失效 |

## 6. 可借鉴实验设置与 metric 归纳

### 6.1 常见模型与任务设置

| 路线 | 常见模型 | 常见任务/数据 |
| --- | --- | --- |
| 多模态压缩/量化 | `Kimi-VL`、`Qwen3-VL`、`DeepSeek-VL2`、`InternVL3.5` | `MMMU`、`MMBench`、`MME-RealWorld`、`InfoVQA`、`OCRBench` |
| 多模态 routing / VLA | 专用 VLA / scene reasoning 模型 | `nuScenes` open-loop、`LangAuto` closed-loop |
| 通用 MoE merge/compress | `Mixtral`、`DeepSeekMoE`、代码/通用生成 MoE | 生成任务、MMLU 类任务、压缩率对比 |

### 6.2 常见 baseline 家族

1. `role-agnostic merge`：`HC-SMoE`、`MergeMoE`、`PuzzleMoE`
2. `prune`：`REAP`、drop-only 变体
3. `repair after compression`：`Router KD`
4. `multimodal compression but not merge`：`FastMMoE`、`VEQ`、`Quant Experts`

### 6.3 值得优先复用的指标

| 指标类别 | 指标 | 作用 |
| --- | --- | --- |
| 主性能 | benchmark accuracy / score retention | 判断相同压缩率下是否保住多模态能力 |
| 效率 | FLOPs、latency、memory footprint | 证明 merge 不是只换叙事不换收益 |
| 结构诊断 | routing consistency、bridge routing retention、expert output reconstruction error | 这是你最需要补的证据层 |
| 任务敏感性 | OCR/InfoVQA/复杂 reasoning 子集 | 更容易暴露 bridge path 退化 |

### 6.4 最值得借鉴的分析方式

1. `VEQ` 的模态梯度与 expert activation 分析：可用于支持 role discovery 的先验。
2. `FastMMoE` 的 routing-aware 冗余诊断：可用于构造 bridge-sensitive token / expert 指标。
3. `Router KD` 的压缩前后 router mismatch 诊断：可用于证明你的收益不只是 router 修补。
4. `MergeMoE` 的同压缩率公平比较：可作为最关键主表的比较协议。

## 7. 当前文献中的空白与未充分回答的问题

1. `文献明确支持`：已有工作承认多模态里存在模态不对称、专家异质性、routing signal 失配。
2. `文献明确支持`：已有 merge 论文强调更好的合并目标或更好的压缩矩阵。
3. `我的推断`：但还缺一条线专门回答：
   - 哪些 experts 在多模态里根本不应该互相比较？
   - 哪些 experts 承担 bridge role？
   - bridge-sensitive 退化是否可以在 merge 前被规避，而不是 merge 后才补救？
4. `我的推断`：这正是 `BridgePreserve` 最可能占住的位置。

## 8. 阶段性判断

- `当前最像哪类已有工作`：多模态异质性 + merge 约束 的交叉题，而不是纯 merge 算法题。
- `最危险的比较对象`：`MergeMoE`、`Router KD`、`VEQ`。
- `最可借鉴的实验模板`：`FastMMoE` 的多模态效率设定 + `MergeMoE` 的公平压缩率比较 + `Router KD` 的 mismatch 诊断。
