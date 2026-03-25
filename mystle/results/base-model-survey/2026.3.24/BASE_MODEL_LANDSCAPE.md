# 多模态基础模型实现路线：Landscape 速览

**日期**：2026-03-24  
**主题**：多模态 base/foundation 模型的工程实现路线 taxonomy 与代表工作脉络  
**使用说明**：本文档用于 survey 阶段的路线归类与演进对照；结论区分文献事实与推断，便于后续精读论文或代码时校准。

---

## 1. 实现路线 Taxonomy（文献已验证）

下列分类依据公开论文/技术报告/开源仓库中**明确描述**的架构范式归纳；边界处存在混合设计（如「projector + 部分交叉注意力」），表中按**主导机制**归类。

| 代号 | 路线名称 | 核心机制（简述） | 典型代表 |
|------|----------|------------------|----------|
| A | 冻结视觉编码器 + cross-attention 注入 | 视觉特征经 perceiver/Gated XATTN 等注入冻结 LLM 层间 | Flamingo 系列 |
| B | 冻结双塔 + Q-Former / query bridge | 可学习 query 从视觉侧抽取固定长度表征再喂 LLM | BLIP-2、InstructBLIP |
| C | 外部编码器 + projector 对齐 | ViT/SigLIP 等 + linear/MLP 等映射到 LLM 嵌入空间 | LLaVA、Idefics2、InternVL、Pixtral |
| D | LLM 内部结构视觉专门化 | 在 Transformer 内部增加视觉专家/专用注意力路径 | CogVLM |
| E | Unified token / 原生自回归 | 图像/文本等统一为离散 token 或共享词表自回归 | Kosmos-1/2、Chameleon、Emu3、Fuyu |
| F | 动态分辨率与长上下文友好 | 多尺度 patch、窗口/池化、多图序列组织以控 token | Qwen2-VL、Qwen2.5-VL、LLaVA-NeXT/Interleave |
| G | 音频/视频扩展 | 时间维编码 + 与 LLM 对齐的连接器或统一序列 | Video-LLaVA、Qwen2-Audio |

**推断**：路线 F、G 与 A–E 常**组合出现**（例如 C+F）；「一条模型只属于一类」的划分在工程上过于理想化。  
**置信度：高**（组合趋势）；**低**（个别闭源细节）。

---

## 2. 代表模型覆盖（约 20 个，适度合并系列）

| 系列/模型 | 主导路线 | 备注（机制焦点，非排行榜） |
|-----------|----------|----------------------------|
| CLIP | 预训练骨干（对比学习） | 常为后续 C 类提供视觉塔 |
| SimVLM | 前缀式多模态预训练 | 早期「接口+自回归」探索 |
| PaLI / PaLM-E 思路 | 大规模多任务 | 文献已验证：编码器+LLM 规模化 |
| Flamingo | A | 交叉注意力注入、冻结骨干 |
| BLIP-2 / InstructBLIP | B | Q-Former + 冻结双塔 |
| MiniGPT-4 | C（早期开源配方） | projector + Vicuna 类 LLM |
| LLaVA-1.0 / 1.5 | C | 线性/MLP projector 成为开源标配 |
| mPLUG-Owl | C + 模块化 | 多阶段与模块命名在开源生态可对照 |
| CogVLM | D | 内部视觉专家路径 |
| Kosmos-1 / Kosmos-2 | E | 统一自回归与 grounding 相关设计 |
| Fuyu-8B | E | 原生矩形 patch 序列，弱化传统 ViT+projector 叙事 |
| IDEFICS / IDEFICS2 | C（及工程化数据管线） | HuggingFace 生态代表 |
| Qwen-VL → Qwen2-VL / Qwen2.5-VL | C + F | 动态分辨率、位置与长序列组织 |
| LLaVA-NeXT / Interleave | C + F | 多图、高分辨率与数据配方 |
| Chameleon | E | 统一 token 自回归 |
| Video-LLaVA | G | 视频帧编码 + LLM 对齐 |
| InternVL 2.x | C | 强视觉塔 + projector/对齐策略 |
| Pixtral 12B | C | 公开材料中的 SigLIP + 多模态 LLM |
| Emu3 | E | 离散视觉 token + 统一生成 |
| Qwen2-Audio | G | 语音/音频编码与对齐 |

**文献已验证**：上表「路线」均可在对应论文或官方技术说明中找到直接描述。  
**推断**：「开源标准配方」指 LLaVA 系在社区复现广度上的影响，非唯一技术真理。  
**置信度：高**（路线归属）；**中**（个别闭源细节）。

---

## 3. 演进脉络（推断为主，辅以共识）

| 阶段 | 焦点 | 代表共识 |
|------|------|----------|
| 接口定义期 | 如何在**不破坏**预训练 LLM 的前提下接入视觉 | Flamingo、BLIP-2 将问题表述为桥接与注入 |
| 开源配方期 | 低门槛复现：**冻结编码器 + projector + 指令数据** | LLaVA 系扩散 |
| 统一与规模化期 | 动态分辨率、多图多帧、长视频、原生统一 token | Qwen2-VL、Chameleon、Emu3 等方向并行 |

**推断**：近期趋势是「控制序列长度 + 提高信息密度」与「统一建模」两条线并进。  
**置信度：中**。

---

## 4. 横向对照：本 survey 关注点

| 维度 | 本备忘录强调 | 刻意弱化 |
|------|--------------|----------|
| 目标 | 编码器—连接器—LLM 的**共性机制** | SOTA 榜单名次 |
| 方法 | Taxonomy、对照表、可迁移概念 | Instruction trick 细节 |

---

## 5. 小结

- **文献已验证**：A–G 七类路线足以覆盖当前公开论述的主流实现范式；上表约 20 个代表点可支撑后续 mechanism 深挖。  
- **推断**：未来 1–2 年「F+G」与「E」的竞争与融合会继续加强。  
- **置信度：高**（taxonomy 可用性）；**中**（演进速度）。
