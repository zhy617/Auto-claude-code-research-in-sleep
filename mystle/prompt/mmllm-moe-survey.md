请严格按下面的 pipeline 一次性执行，不要中途停下询问我；如果需要写文件，就直接在当前工作区创建。
@skills/research-lit/SKILL.md
@skills/idea-creator/SKILL.md
@skills/novelty-check/SKILL.md
@skills/research-review/SKILL.md

你现在是我的“研究选题代理”，请在一个会话内连续执行完整 idea-only pipeline，禁止在中间停下来等我确认。

## 研究背景
- 我之前做的是纯文本 MoE 的专家合并，目标是提高推理速度。
- 现在研究方向是：多模态模型（VLM/MLLM）下的 MoE 专家合并新挑战。

## 全局约束
1) 只做 idea 与论文方向判断，不做任何实验执行
2) 禁用 mcp__codex__* / mcp__llm-chat__*；允许使用 mcp__obsidian-vault__* 仅用于读取/写入我的 Obsidian 笔记
3) 不需要 GPU、部署、训练脚本
4) 优先利用我已有材料（尤其是我 Obsidian 里面的 90_Project 里的结果）
5) 不要中途提问，不要等待确认；信息不足时做合理假设并继续
6) 每阶段都写文件，保证可恢复和可复用
7) 每阶段产出除本地文件外，还需同步一份到 Obsidian：93_Research_Records/IdeaPipeline/{{date}}

## 执行阶段（必须按顺序）
### Phase 1: Literature Landscape
- 主题：多模态 MoE 专家合并的新挑战
- 重点比较：纯文本 MoE 合并 vs 多模态 MoE 合并的差异（expert specialization、router behavior、modality imbalance、跨模态对齐等）
- 识别现有 merge/prune/distill/compress 方法在多模态中的失效点
- 输出文件：IDEA_LANDSCAPE.md
- 文件内容要求：
  - 10-20 篇代表工作表格
  - challenge taxonomy（>=5类）
  - 3个最值得做的问题定义
  - 明确标注“文献已验证”与“推断”

### Phase 2: Idea Generation & Ranking
- 基于 IDEA_LANDSCAPE.md 生成 10 个 idea，筛到 Top-3
- 偏好：延续“专家合并提升推理速度”主线，但必须体现多模态特有问题
- 避免低新颖度“文本方法直接迁移”
- 评分权重：新颖性40%，可落地性30%，论文价值30%
- 输出文件：IDEA_REPORT.md
- 每个 idea 需包含：核心假设、方法直觉、与最近工作差异、潜在审稿风险、最小纸面验证路径
- 同时给出淘汰的7个 idea 及淘汰理由

### Phase 3: Novelty Check for Top-3
- 对 IDEA_REPORT.md 的 Top-3 分别做 novelty check（重点 2024-2026）
- 仅基于公开检索进行 novelty check；Obsidian MCP 只用于记录检索结论与引用，不作为外部检索来源
- 输出文件：
  - NOVELTY_IDEA_1.md
  - NOVELTY_IDEA_2.md
  - NOVELTY_IDEA_3.md
- 每份包含：最接近工作Top5、机制级差异、novelty 风险评级（高/中/低）、投稿叙事建议

### Phase 4: Final Selection (Reviewer-style)
- 综合 IDEA_REPORT + 三份 novelty 文件，做最终选题评审
- 输出文件：FINAL_IDEA_SELECTION.md
- 必含：
  1. 推荐主线 idea（1个）+ 备选（1个）
  2. 每个的 killer weakness
  3. 最小可 defend 的核心 claim（一句话）
  4. 150-200字论文卖点摘要
  5. 接下来两周的“纯想法阶段”工作清单（不含实验）

## 结束要求
- 最后在聊天中仅输出：
  - 产出文件清单
  - 主线与备选 idea 名称
  - 一段简短结论（<=120字）
