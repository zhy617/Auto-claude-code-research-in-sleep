# 02 CLOSEST PAPERS SYNTHESIS

日期：2026-03-25  
目标：精读与你的 idea 最接近、最危险的 5 篇论文，并给出跨论文综合判断。

## 0. 选择这 5 篇的理由

我选的是：

1. `MergeMoE`：最接近的 expert merge 主线
2. `VEQ`：最直接公开写出 `modality discrepancy + expert heterogeneity`
3. `Router KD`：最直接解释压缩后 `router-expert mismatch`
4. `SAMoE-VLA`：最直接证明文本 MoE routing 迁移到多模态会失效
5. `FastMMoE`：最接近你可以复用的多模态效率实验与路由分析框架

其中：

- `文献明确支持`：来自论文摘要页、VEQ HTML 正文、你本地已有 `FastMMoE` 精读结果。
- `我的推断`：来自对论文设计意图与潜在 flaw 的归纳。

## 1. Paper 1: MergeMoE

### 1.1 Task

- `文献明确支持`：目标是压缩通用 MoE 模型，在相同压缩率下比已有 merging baselines 保留更多性能。
- 更形式化地说，它研究的是：给定原始 experts，如何构造 merged experts，使 merged outputs 逼近原始 experts 的输出集合。

### 1.2 Challenge

- 旧 merge 方法常把问题理解成参数平均或启发式聚类，理论支撑弱。
- merge 之后若输出逼近不足，性能损失会直接暴露。
- `我的推断`：它的默认前提是“被聚到一起的 experts 至少在同一个输出比较空间里”。

### 1.3 Inspiration

- `文献明确支持`：核心启发是把 merge 从“参数聚合”改写成“输出合并”。
- `我的推断`：作者是在回应早期 merge 方法理论基础不够的问题，而不是在质疑 expert 可比性本身。

### 1.4 Insight & Novelty

- 核心 insight：expert merging 可以理解成在前向计算中插入额外矩阵，从而自然落到一个优化问题。
- 真正 novelty：
  - 不是“发现 experts 不同”，而是“在 experts 已可比的前提下，如何更优地压缩它们的输出子空间”。

### 1.5 Potential Flaw

- `我的推断`：如果进入多模态 setting，而 experts 的角色本身不可比，那么更好的 output merge 可能只是“在错误候选集上做更优优化”。
- 它并不回答：哪些 experts 根本不该被放进同一 merge bucket。

### 1.6 为什么它危险

- 因为审稿人会自然问：为什么不直接在 `MergeMoE` 前面加一个 role grouping，就够了？
- 所以你的论文不能停留在“先分组，再 merge”，而要证明：
  - 分组本身是核心科学问题；
  - bridge protection 的收益不是任何 merge 算子都自动拥有的。

## 2. Paper 2: VEQ

### 2.1 Task

- `文献明确支持`：研究如何对 MoE Vision-Language Models 做 post-training quantization，同时保持性能。
- 它不是 merge 论文，但它直接处理 MoE VLM 压缩中的结构不均衡问题。

### 2.2 Challenge

- `文献明确支持`：既有 quantization 方法把模型当成单体 dense 结构，忽视两类异质性：
  - `modality discrepancy`
  - `expert heterogeneity`
- `文献明确支持`：VEQ 还指出 text tokens 的梯度影响远高于 vision tokens，且 experts 存在 generalist / modality-specific 分工。

### 2.3 Inspiration

- `文献明确支持`：来自对 vision/text sensitivity gap、expert activation imbalance、router affinity 的分析。
- `我的推断`：它隐含在说，多模态压缩里“统一策略”是错的。

### 2.4 Insight & Novelty

- 核心 insight：多模态 MoE 压缩必须同时考虑模态差异与 expert 重要性。
- 真正 novelty：
  - 通过 `Modality-Expert-Aware Quantization`
  - 以及 `Modality-Affinity-Aware Quantization`
  把这种不对称性写进 quantization objective。

### 2.5 Potential Flaw

- 研究对象是 quantization，不是 merge。
- `我的推断`：它证明了“异质性存在”，但并未证明“bridge experts 必须单独保护”。

### 2.6 为什么它危险

- 因为它已经公开占据了“多模态异质性不可忽略”的话语权。
- 你如果只说“vision/text experts 不一样”，审稿人会认为这是 VEQ 的 setting transfer。

## 3. Paper 3: Router KD

### 3.1 Task

- `文献明确支持`：研究 retraining-free MoE compression 后的性能退化，主张核心原因是 `router-expert mismatch`。
- 方法上只更新极少量 router 参数，用 KD 恢复原模型 next-token distribution。

### 3.2 Challenge

- 压缩后 experts 变了，但 router 没变，导致选错专家。
- 过去很多方法只动 experts，不动 router，因此出现系统性退化。

### 3.3 Inspiration

- `文献明确支持`：作者把已有 compression methods 统一进 pruning / editing / merging 三类范式，再指出它们共同遗漏了 router calibration。
- `我的推断`：它的真正立场是“压缩本身不是罪，router 不同步才是罪”。

### 3.4 Insight & Novelty

- 核心 insight：在 retraining-free compression 中，最值得更新的不是专家本体，而是 router。
- 真正 novelty：只校准 router 就能稳定回收一部分性能。

### 3.5 Potential Flaw

- 它不回答“哪些 experts 不该被动”。
- `我的推断`：如果桥接子空间已经被错误合并，router calibration 可能只能恢复选择概率，无法恢复被抹平的函数角色。

### 3.6 为什么它危险

- 审稿人最容易把你的收益解释成：你只是减少了 router mismatch。
- 因此你必须设计一个对照：
  - `naive merge + Router KD`
  - `same-role merge`
  - `same-role + bridge preserve`
  用来证明前两者并不等价。

## 4. Paper 4: SAMoE-VLA

### 4.1 Task

- `文献明确支持`：研究 Vision-Language-Action 中，为什么 LLM 继承来的 token-level MoE 机制会在自动驾驶场景中不稳定。
- 它的解决方案是用 scene-level structured representation 生成 routing signal。

### 4.2 Challenge

- `文献明确支持`：直接套用 token-level MoE routing 会带来性能不稳与安全退化。
- 核心难点不是“怎么多加 experts”，而是“什么信号才适合作为多模态/VLA 的 routing basis”。

### 4.3 Inspiration

- `文献明确支持`：来自对自动驾驶场景的经验分析，发现场景级决策与 token 粒度的专家专门化不对齐。
- `我的推断`：这类论文其实在告诉我们，多模态专家的角色组织单位可能比 token 粒度更高阶。

### 4.4 Insight & Novelty

- 核心 insight：多模态/VLA 中 routing 粒度与信号本身需要重定义。
- 真正 novelty：以 BEV scene context 驱动 routing，而不是直接沿用 token embedding。

### 4.5 Potential Flaw

- 设置偏自动驾驶 VLA，不是通用 VLM merge。
- `我的推断`：不能直接证明 bridge expert 的存在，但能强力支持“文本 MoE routing 假设不能直接迁移到多模态”。

### 4.6 为什么它危险

- 如果你的故事过度依赖 router-based role discovery，审稿人可能会说：既然 routing signal 本身可能错，那 role discovery 也不稳。
- 这逼着你在 role discovery 上不能只依赖单一 router 统计，要加入跨模态共激活或输出敏感度证据。

## 5. Paper 5: FastMMoE

### 5.1 Task

- `本地笔记支持`：目标是在 MoE-based MLLM 上，对高分辨率视觉 token 带来的高 FLOPs / 延迟问题做 training-free 加速。
- 它同时压两个杠杆：
  - 视觉 token 数
  - 每个视觉 token 激活的专家数

### 5.2 Challenge

- dense VLM pruning 方法只看到 token redundancy，看不到 expert redundancy。
- 在 MoE-MLLM 里，压 token 会扰动 routing；压 experts 又可能破坏文本侧和细粒度视觉能力。

### 5.3 Inspiration

- `本地笔记支持`：作者观察到视觉 token 在路由空间更冗余、激活更多 experts 但贡献不一定必要。
- `本地笔记支持`：文本 token 更敏感，视觉 token 更有冗余空间。

### 5.4 Insight & Novelty

- 核心 insight：MoE-MLLM 的视觉冗余应从 routing / expert activation 视角理解，而不是继续沿用 dense attention 视角。
- 真正 novelty：
  - 动态 expert activation reduction
  - routing-aware token pruning
  - 联合建模冗余度与重要度

### 5.5 Potential Flaw

- 它不做 expert merge。
- `我的推断`：但它给出一个很危险的替代解释，即多模态效率收益也许主要来自“模态不对称预算”，不一定需要提出 bridge-preserving merge。

### 5.6 为什么它重要

- 它提供了你最值得复用的实验与分析模板：
  - 用多模态 benchmark 做性能保持率
  - 用 routing-aware 指标解释性能来源
  - 强调视觉侧更冗余、文本侧更敏感

## 6. 跨论文对照表

| 论文 | 它真正解决了什么 | 它没有解决什么 | 对 `BridgePreserve` 最危险的地方 |
| --- | --- | --- | --- |
| MergeMoE | 如何更好地 merge 可比 experts | 哪些 experts 可比 | 会把你的贡献压成“前处理分组” |
| VEQ | 如何把异质性写进多模态压缩 objective | merge admissibility、bridge role | 会抢走“异质性已知”这部分 novelty |
| Router KD | 压缩后如何轻量修 router | 哪些 experts 不该动 | 会把你的收益解释成 router calibration |
| SAMoE-VLA | 多模态 routing signal 为什么会失效 | merge 与 bridge preservation | 会质疑你 role discovery 的信号可靠性 |
| FastMMoE | 多模态 MoE 的效率瓶颈可从 routing 角度分析 | expert merge 约束 | 会提供一个更直接的 efficiency baseline |

## 7. 哪些机制 / 实验设计最值得复用

### 7.1 机制层面

1. 从 `VEQ` 复用：
   - 模态梯度不对称分析
   - expert activation / affinity 分析
2. 从 `FastMMoE` 复用：
   - routing-aware redundancy 分析
   - 视觉侧更激进压缩、文本侧更保守的设计直觉
3. 从 `Router KD` 复用：
   - compression 前后 router mismatch 诊断
4. 从 `MergeMoE` 复用：
   - 同压缩率公平比较协议

### 7.2 实验层面

1. 基座模型优先选已出现在多模态压缩论文中的 MoE-MLLM，如 `DeepSeek-VL2` 或 `InternVL3.5`。
2. 任务至少覆盖：
   - 一个综合多模态理解 benchmark
   - 一个 bridge-sensitive benchmark，如 OCR / InfoVQA /复杂跨模态推理
3. 结果不能只报主分数，还要报：
   - 路由保持率
   - merge 后输出重建误差
   - 被保护 experts 的使用分布变化

## 8. 哪些坑必须避开

1. 把 `bridge expert` 当作只靠人工命名的概念。
2. 只展示平均 benchmark retention，而不展示 bridge-sensitive 子任务。
3. 只和简单 merge baseline 比，不和 `Router KD`、`FastMMoE` 这种邻近强基线比。
4. 把贡献写成“我们发现多模态异质性存在”，因为这已经被 `VEQ` 一类工作占掉。
5. 把论文写成“merge 后再修 router”，否则会被 `Router KD` 吞掉。

## 9. 综合判断

- `当前最值得复用的机制`：`VEQ` 的异质性分析 + `FastMMoE` 的多模态效率协议 + `Router KD` 的 mismatch 诊断。
- `当前最危险的先行工作`：`MergeMoE`。
- `当前最该避开的伪新颖点`：把 role-aware 仅实现成一套 heuristic merge ratio 规则。
