# 04 MINIMUM EXPERIMENT PLAN

日期：2026-03-25  
Idea：`BridgePreserve`

## 0. Planning principle

- 目标不是做完整论文，而是判断这条线是否值得继续投入。
- 按 `experiment-plan` 的要求，先冻结 `1 个 primary claim + 1 个 supporting claim`。
- 所有实验都围绕 “是否改变 reviewer 信念” 来设计。

## 1. Claim Map

| Claim | Why It Matters | Minimum Convincing Evidence | Linked Blocks |
| --- | --- | --- | --- |
| C1 Primary | 多模态 merge 的核心问题是错误的可比性假设；`same-role merge + bridge preserve` 比 `role-agnostic merge` 更稳 | 在相同压缩率下，bridge-sensitive 任务保留率显著更高，且收益不能用更少 merge 数量解释 | B1, B2 |
| C2 Supporting | bridge-aware 保护的收益，不等价于 router-only calibration | `same-role + bridge preserve` 优于 `naive merge + Router KD`，尤其在 bridge-sensitive 指标上 | B2, B3 |
| Anti-claim | 所有收益只来自“压得更少”或“修 router” | 控制总压缩率、对比 `Router KD`、报告被保护 experts 数量 | B1, B2 |

## 2. Freeze the paper story

### Primary claim

在多模态 MoE 中，expert merge 不应先问“怎么合并得更好”，而应先问“哪些 experts 允许被合并”；当显式保护 bridge experts 时，在相同压缩率下可保留更多跨模态能力。

### Supporting claim

bridge-aware 的收益不能被 `router mismatch recovery` 完全替代。

### Minimum convincing evidence

1. `same-role merge` 在相同压缩率下优于 `role-agnostic merge`。
2. `same-role + bridge preserve` 进一步优于 `same-role without bridge protection`。
3. `naive merge + Router KD` 仍无法追平 `same-role + bridge preserve`。

## 3. Experimental Storyline

- Main paper must prove:
  - merge 单位需要 role-aware
  - bridge protection 确实重要
- Appendix can support:
  - 更复杂的 role discovery 版本
  - 更多 benchmark / 更多压缩率
- Experiments intentionally cut:
  - 大规模多模型泛化
  - 复杂训练式 bridge distillation
  - 过多可解释性可视化

## 4. Experimental Blocks

### Block B0: Role Proxy Sanity Check

- Claim tested：`bridge experts` 至少能被一个简单、可复现的 proxy 识别出来
- Why this block exists：没有这个 block，整条线没有定义基础
- Dataset / split / task：
  - calibration set：`200-500` 条 image-text 样本
  - 最好覆盖一般 VQA + OCR/InfoVQA 风格样本
- Compared systems：
  - proxy A：按视觉/文本 token 路由比例划分
  - proxy B：按跨模态共激活强度划分
  - proxy C：按 merge 后局部性能敏感度划分
- Metrics：
  - 不同 proxy 下 expert role 分布稳定性
  - 不同样本子集上的一致性
  - bridge-sensitive token / task 的富集程度
- Setup details：
  - 基座优先选你已有推理管线最容易接入的 MoE-MLLM；首选 `DeepSeek-VL2` 或 `InternVL3.5`
  - 不训练，只做 calibration forward
- Success criterion：
  - 至少有一种 proxy 能稳定找出一小批高敏感 experts
- Failure interpretation：
  - 如果 proxy 极不稳定，说明当前 idea 还不适合直接做 merge 论文
- Table / figure target：
  - Figure 1：expert role / bridge score 分布图
- Priority：`MUST-RUN`

### Block B1: Main Anchor Result

- Claim tested：`same-role merge + bridge preserve` 在相同压缩率下比 `role-agnostic merge` 更稳
- Why this block exists：这是最核心的 reviewer belief change
- Dataset / split / task：
  - 一个综合 benchmark：`MMMU` 或 `MMBench` 的小规模 dev subset
  - 一个 bridge-sensitive benchmark：`InfoVQA` 或 `OCRBench` 小规模子集
- Compared systems：
  - Uncompressed base
  - `role-agnostic merge`
  - `same-role merge`
  - `same-role + bridge preserve`
- Metrics：
  - accuracy / score retention
  - memory / FLOPs / latency
  - compression ratio
- Setup details：
  - 固定总压缩率，例如 `25%` 或 `50%` expert reduction
  - merge 算子先固定为最简单可复现版本，不在这里比较复杂 merge formula
  - seed 若有随机聚类则用 `3 seeds`
- Success criterion：
  - 在相同压缩率下，`same-role + bridge preserve` 相对 `role-agnostic merge` 在 bridge-sensitive 任务上有稳定正收益
  - 同时效率收益没有明显倒退
- Failure interpretation：
  - 若只在综合分数上波动但 bridge-sensitive 任务无收益，说明“bridge”叙事不成立
- Table / figure target：
  - Table 1：主结果表
- Priority：`MUST-RUN`

### Block B2: Novelty Isolation

- Claim tested：真正关键的是 bridge protection，而不只是 role grouping 或压得更少
- Why this block exists：用来拆掉 “只是 heuristic merge ratio” 的质疑
- Dataset / split / task：
  - 与 B1 相同，但可以只保留 bridge-sensitive 子集以省算力
- Compared systems：
  - `same-role merge`
  - `same-role + protected top-k bridge experts`
  - `same-role + random protect`
  - `same-role + protect high-frequency experts`
- Metrics：
  - bridge-sensitive 性能保持率
  - 被保护 experts 的使用分布变化
  - merge 后输出重建误差
- Setup details：
  - 固定被保护 experts 数量，排除“只是保留更多参数”的混淆
- Success criterion：
  - bridge-aware protection 优于 random/high-frequency protection
- Failure interpretation：
  - 若 random protect 也一样好，说明 bridge 定义没抓到结构本质
- Table / figure target：
  - Figure 2：不同保护策略对比
- Priority：`MUST-RUN`

### Block B3: Router-vs-Structure Check

- Claim tested：bridge-aware 收益不等价于 router-only calibration
- Why this block exists：防止被 `Router KD` 吞掉
- Dataset / split / task：
  - 与 B1 一致，但可以缩小到单一 benchmark 子集
- Compared systems：
  - `naive merge`
  - `naive merge + Router KD`
  - `same-role + bridge preserve`
- Metrics：
  - 主任务分数
  - routing consistency
  - bridge routing retention
- Setup details：
  - 如果实现 `Router KD` 成本过高，可先用轻量 router-only 校准近似替代，并明确写为近似
- Success criterion：
  - `same-role + bridge preserve` 至少在 bridge-sensitive 指标上优于 router-only recovery
- Failure interpretation：
  - 若 `Router KD` 可完全追回收益，则论文应重构成“bridge-aware router calibration”而不是 merge paper
- Table / figure target：
  - Table 2：novelty isolation against router repair
- Priority：`MUST-RUN`

### Block B4: Failure Analysis

- Claim tested：性能收益来自保护桥接链路，而不是偶然噪声
- Why this block exists：给 reviewer 机制证据
- Dataset / split / task：
  - 20-50 个定性样例
- Compared systems：
  - base
  - naive merge
  - final method
- Metrics：
  - bridge-sensitive case breakdown
  - routing heatmap / expert usage shift
  - qualitative failure categories
- Setup details：
  - 只分析最有代表性的样例，不做大规模可视化
- Success criterion：
  - 能看到 final method 相比 naive merge 更少出现跨模态对齐断裂
- Failure interpretation：
  - 若分析无法解释收益来源，论文会更像黑箱 heuristic
- Table / figure target：
  - Figure 3：failure case / routing shift
- Priority：`NICE-TO-HAVE`

## 5. Main Experiment Block

### 最小数据 / 任务设置

1. 一个 MoE-MLLM 基座：优先 `DeepSeek-VL2` 或 `InternVL3.5`
2. 一个小 calibration set：`200-500` 样本
3. 两个最小评测集：
   - `MMMU/MMBench` 小规模子集，用于总体能力
   - `InfoVQA/OCRBench` 小规模子集，用于 bridge-sensitive 能力

### 最关键 baseline

1. `role-agnostic merge`
2. `same-role merge`
3. `naive merge + Router KD` 或 router-only recovery 近似

### 最关键 metric

1. bridge-sensitive performance retention
2. same compression ratio 下的效率收益
3. bridge routing retention / reconstruction error

### 成功标准

- 在固定压缩率下，`same-role + bridge preserve` 对 `role-agnostic merge` 的优势是稳定且可解释的。
- 相比 router-only recovery，bridge-sensitive 指标仍有额外收益。

### 失败后如何解释

- 若 `same-role` 有效但 `bridge preserve` 无效：
  - 说明题目应缩成 `role-aware merge`，不要强卖 bridge。
- 若 `Router KD` 完全追回收益：
  - 说明核心问题可能是 router mismatch，不是 merge admissibility。
- 若 role proxy 本身不稳定：
  - 说明当前缺少可操作定义，这条 idea 暂不宜进入方法论文阶段。

## 6. Novelty Isolation Block

- 必做对照：`bridge preserve` vs `random protect` vs `protect high-frequency experts`
- 核心目的：证明被保护的是“桥接结构”，不是“重要但普通”的 experts

## 7. Failure Analysis Block

- 应优先记录三类失败：
  1. OCR / text-in-image 错误
  2. image-text cross-reference 错误
  3. 复杂多步 reasoning 中视觉证据丢失
- 这些样例最能暴露 bridge path 是否被破坏。

## 8. Run Order

| Milestone | Goal | Runs | Decision Gate | Cost | Risk |
| --- | --- | --- | --- | --- | --- |
| M0 | 搭好 calibration 与 role proxy 统计 | B0 | 若 proxy 完全不稳，停 | 低 | 定义不硬 |
| M1 | 建立 `role-agnostic merge` 与主评测脚本 | baseline runs | 若 baseline 不可复现，停 | 中 | 工程阻塞 |
| M2 | 跑 `same-role merge` 与 `same-role + bridge preserve` | B1 | 若无任何正向趋势，谨慎止损 | 中 | idea 可能不成立 |
| M3 | 跑 novelty isolation 与 router 对照 | B2 + B3 | 若优势可被 random 或 Router KD 解释，重构题目 | 中 | novelty 被吞 |
| M4 | 做少量 failure analysis | B4 | 仅在前面有信号时继续 | 低 | 分析价值不足 |

## 9. Must-run vs Nice-to-have

### Must-run

1. B0 Role Proxy Sanity Check
2. B1 Main Anchor Result
3. B2 Novelty Isolation
4. B3 Router-vs-Structure Check

### Nice-to-have

1. B4 Failure Analysis
2. 更多模型泛化
3. 更多压缩率扫描

## 10. Go / No-Go Criteria

### Go

- role proxy 稳定
- `same-role + bridge preserve` 在固定压缩率下优于 `role-agnostic merge`
- 优势不能被 random protect 或 router-only recovery 解释

### No-Go

- bridge expert 定义不稳定
- 所有收益都可由 `same-role` 或 `Router KD` 吃掉
- bridge-sensitive 任务没有稳定提升

## 11. Biggest Risk and Mitigation

- Biggest risk：
  - `bridge expert` 最终无法被硬定义，导致贡献塌成 heuristic
- Mitigation：
  - 第一阶段不要追求复杂方法，先用最简单 proxy 证明“保护某一小批 experts 有结构性收益”
  - 如果这一步都不成立，尽早止损

## 12. Final recommendation

- 当前最值得先跑的不是完整 merge 论文实验，而是：
  - `固定总压缩率` 下的 `role-agnostic merge` vs `same-role merge` vs `same-role + bridge preserve`
- 这是信息增益最高、最能决定去留的最小实验包。
