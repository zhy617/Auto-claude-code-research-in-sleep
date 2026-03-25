请严格按下面的 pipeline 一次性执行，不要中途停下询问我；如果需要写文件，就直接在当前工作区创建。
@skills/research-lit/SKILL.md
@skills/research-review/SKILL.md

你现在是我的“多模态基础模型实现调研代理”，请在一个会话内连续执行完整 survey pipeline，禁止在中间停下来等我确认。

## 研究背景
- 我接下来想做多模态模型方向的优化，但在此之前需要先真正理解：如今主流多模态 base 模型到底是如何实现多模态能力的。
- 我更关心“机制与实现路径”而不只是 benchmark 排名，包括：视觉/音频编码器如何接入 LLM、跨模态对齐怎么做、token 是怎么组织的、训练分阶段怎么设计、不同架构各自的瓶颈是什么。
- 本次任务的目标不是提出新 idea，而是形成一份可直接支撑后续优化选题的“实现机制地图”。

## 全局约束
1) 本次只做 survey / taxonomy / mechanism analysis，不做实验执行
2) 禁用 mcp__codex__* / mcp__llm-chat__*；允许使用 mcp__obsidian-vault__* 仅用于读取/写入我的 Obsidian 笔记
3) 不需要 GPU、部署、训练脚本，不要求复现代码
4) 优先关注 2023-2026 的代表性多模态 base 模型与综述，但需要补上关键奠基模型
5) 不要中途提问，不要等待确认；信息不足时做合理假设并继续
6) 每阶段都写文件，保证可恢复和可复用，产出文件放到 ./mystle/results/base-model-survey/{{date}} 下
7) 每阶段产出除本地文件外，还需同步一份到 Obsidian：93_Research_Records/BaseModelSurvey/{{date}}
8) 结论必须区分“论文明确写到的机制”与“基于论文/实现细节的合理推断”
9) 优先分析 base model / foundation model 层面的共性机制，不把精力放在 instruction tuning tricks 或 benchmark engineering 上

## 执行阶段（必须按顺序）
### Phase 1: Model Landscape
- 主题：主流多模态 base 模型的实现路线图
- 覆盖模态：至少包含 vision-language；若资料充分，可补充 audio-language / video-language 的代表模型
- 重点关注：
  - LLM + 外部视觉编码器 + projector 路线
  - unified token / native multimodal autoregressive 路线
  - cross-attention / resampler / Q-former / adapter / projector 等连接结构
  - 不同模型如何处理高分辨率、多图、多帧、长上下文
- 输出文件：BASE_MODEL_LANDSCAPE.md
- 文件内容要求：
  - 15-25 个代表模型表格
  - 每个模型至少包含：年份、模态、backbone、视觉/音频编码器、连接模块、token 组织方式、训练阶段、是否开源、核心特点
  - 给出实现路线 taxonomy（>=5类）
  - 给出一段“从 Flamingo/BLIP-2/LLaVA 到近期模型”的演进脉络
  - 明确标注“文献已验证”与“推断”

### Phase 2: Mechanism Dissection
- 基于 BASE_MODEL_LANDSCAPE.md，对主流实现机制做横向拆解
- 重点回答以下问题：
  - 多模态信息是如何进入语言模型的？
  - 视觉/音频 token 与文本 token 在序列中如何共存？
  - 对齐是依赖 projector、Q-former、cross-attention，还是更原生的统一建模？
  - 训练通常分几阶段？各阶段分别解决什么问题？
  - 为什么很多模型能够“看懂图”，但真正的细粒度 grounding、长视频理解、跨模态推理仍然困难？
- 输出文件：MECHANISM_DISSECTION.md
- 文件内容要求：
  - 从“输入表征 -> 模态编码 -> 连接层 -> token 注入 -> 训练阶段 -> 推理行为”形成完整流程图式说明
  - 总结至少 8 个关键设计变量
  - 每个设计变量都要给出：常见做法、优点、缺点、适用条件、典型代表模型
  - 单列一节说明“为什么这些设计能工作”
  - 单列一节说明“为什么这些设计会成为后续优化瓶颈”

### Phase 3: Scaling Laws & Bottlenecks
- 目标：理解多模态 base 模型在扩展与优化时，最关键的瓶颈来自哪里
- 分析维度至少包括：
  - encoder 与 LLM 的能力错配
  - projector / connector 的信息瓶颈
  - token budget 与视觉分辨率冲突
  - modality imbalance
  - alignment vs generalization trade-off
  - 数据配比与训练 curriculum 问题
  - inference latency / memory / kv cache 压力
- 输出文件：SCALING_AND_BOTTLENECKS.md
- 文件内容要求：
  - 提炼 >=8 类瓶颈
  - 对每类瓶颈说明：出现位置、根本原因、具体表现、已有缓解方向、尚未解决之处
  - 特别对“架构瓶颈”和“训练瓶颈”进行区分
  - 给出一个“哪些问题更适合做模型结构优化，哪些更适合做训练/数据优化”的对照表

### Phase 4: Optimization Entry Points
- 基于前三阶段，输出“未来可优化的位置图谱”，但这里只做方向归纳，不产出具体论文 idea
- 关注：
  - 哪些模块最值得优化
  - 哪些机制已经比较饱和，不适合作为主攻点
  - 哪些优化必须建立在先理解 base model 实现细节的基础上
- 输出文件：OPTIMIZATION_ENTRY_POINTS.md
- 文件内容要求：
  - 给出 10 个潜在优化入口
  - 每个入口需包含：对应模块、为什么重要、当前主流做法、常见失败模式、是否适合做高质量研究问题
  - 对 10 个入口按“研究价值 / 可操作性 / 新颖性空间”排序
  - 最后总结 3 个最值得继续深入阅读源码或论文的方向

### Phase 5: Final Briefing
- 综合前四个文件，给出一份面向后续研究准备的最终 briefing
- 输出文件：FINAL_BASE_MODEL_BRIEFING.md
- 必含：
  1. 你认为最重要的 5 条结论
  2. 一个“多模态 base 模型实现总图”
  3. 目前最主流的实现范式（1-2种）及其核心局限
  4. 对后续优化研究最关键的 5 个先修问题
  5. 一份接下来两周的阅读清单与阅读顺序（论文为主，不做实验）

## 写作要求
- 每个阶段尽量使用表格、taxonomy、对照总结，减少空泛描述
- 结论优先给“机制级解释”，不要只说“某模型效果更好”
- 如果同一模型有论文表述与社区普遍理解不一致，要单独标注
- 若能定位公开实现仓库中的关键模块命名，可以记录，但不要展开代码复现
- 对任何不确定结论，都显式标注置信度（高/中/低）

## 结束要求
- 最后在聊天中仅输出：
  - 产出文件清单
  - 你总结的“当前多模态 base 模型最核心的实现主线”一句话
  - 一段简短结论（<=120字）