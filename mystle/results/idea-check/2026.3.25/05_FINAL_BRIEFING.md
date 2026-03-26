# 05 FINAL BRIEFING

日期：2026-03-25  
Idea：`BridgePreserve`

## 1. 这个 idea 当前最像哪一类已有工作

它当前最像：

- `MergeMoE / HC-SMoE / PuzzleMoE` 这一类 expert merge 压缩工作
- 叠加 `VEQ / FastMMoE / SAMoE-VLA` 这类多模态异质性与 routing mismatch 观察

`一句话说`：  
它不是纯新的 merge 算子题，而是 `多模态 MoE merge 的问题重定义`。

## 2. 它真正可能新的部分是什么

最可能新的不是“多模态 experts 有异质性”，因为这个已有公开支持。  
真正可能新的点是：

1. 把 `expert comparability failure` 明确提出为多模态 merge 的主失败模式。
2. 把 `bridge experts` 作为需要显式保护的结构对象提出并操作化。
3. 证明 `same-role merge + bridge preserve` 的收益，不等价于单纯的 router calibration。

## 3. 哪些点最可能被 reviewer 质疑

### 最高风险

`bridge expert` 如果没有硬定义，会被认为只是一个好听但松散的命名。

### 次高风险

1. 审稿人会说：这只是 `MergeMoE/HC-SMoE` 前面加了一个 grouping heuristic。
2. 审稿人会说：这只是把 `VEQ` 中的异质性观察应用到了 merge。
3. 审稿人会说：真正的问题只是 `Router KD` 那条线已经说过的 router-expert mismatch。

## 4. 最值得先做的 1-2 个最小实验

### 实验 1：主判断实验

固定总压缩率，在同一 MoE-MLLM 上比较：

- `role-agnostic merge`
- `same-role merge`
- `same-role + bridge preserve`

重点看：

- 一个综合多模态 benchmark 的保持率
- 一个 bridge-sensitive benchmark（如 `InfoVQA` / `OCRBench`）的保持率

### 实验 2：novelty 隔离实验

固定被保护 expert 数量，比较：

- `bridge preserve`
- `random protect`
- `protect high-frequency experts`
- 如资源允许，再加 `naive merge + Router KD`

这个实验直接回答：你保护的是不是“桥接结构”，而不是普通重要 expert。

## 5. 总体判断

`建议重构后继续`

### 为什么不是直接“建议继续”

因为当前最大瓶颈不是 related work 不够，而是 `bridge expert` 定义还不够硬。  
如果这一定义做实，这条线有机会成为一篇像样的多模态 MoE merge 论文；如果做不实，它会迅速退化成 heuristic paper。

## 6. 最短行动建议

下一步只做一件事：  
先用最简单、可复现的 proxy 把 `bridge expert` 定义硬起来，再跑最小对照实验。不要先花时间设计复杂 merge 公式。
