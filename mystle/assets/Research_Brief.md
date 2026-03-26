# Research Brief: Multimodal MoE Expert Merging

## Problem Statement
当前 MoE 专家合并（expert merging/pruning）方法主要针对纯文本 LLM 设计，
直接迁移到多模态模型（VLM/MLLM）时存在严重的性能退化。
核心问题是多模态 MoE 中专家的跨模态特化模式、路由行为异质性、
以及模态间的功能耦合（bridge experts）在合并时被破坏。

## Background
- **Field**: 多模态大模型（VLM/MLLM）
- **Sub-area**: MoE 专家合并/压缩，推理加速
- **Key papers I've read**: 
  - MergeMoE, HC-SMoE, PuzzleMoE（纯文本 merge）
  - VEQ, FastMMoE, SAMoE-VLA（多模态 MoE 异质性）
- **What I already tried**: 纯文本 MoE 专家合并提升推理速度
- **What didn't work**: 直接将文本 merge 方法迁移到多模态场景，跨模态性能大幅下降

## Constraints
- **Compute**: 无本地 GPU，实验在远程平台跑
- **Timeline**: 3-4 个月到投稿
- **Target venue**: ICLR 2027 / ICML 2026

## What I'm Looking For
- [x] Improvement on existing method: 多模态 MoE merge
- [x] 延续"专家合并提升推理速度"主线，但体现多模态特有问题

## Domain Knowledge
- 纯文本 MoE 中，experts 的功能分化相对均匀，merge 后 router 可以通过 KD 恢复
- 多模态 MoE 中，部分 experts 充当"桥接"角色（bridge experts），连接视觉和语言模态
- BridgePreserve 假设：保护这些 bridge experts 是多模态 merge 成功的关键

## Non-Goals
- 不做模型训练/预训练方法
- 不做纯 router 设计
- 不做与 merge 无关的多模态方法

## Existing Results
- idea-check 初步结论：BridgePreserve 方向可行但需重构
- bridge expert 定义还不够硬，需要更形式化的定义