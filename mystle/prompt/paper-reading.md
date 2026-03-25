@skills/paper-reading/SKILL.md

请使用 `paper-reading` 这个 skill，按其中定义的流程，对这篇论文进行精读和结构化分析：

论文输入：
["C:\Users\13183\Zotero\storage\BJUGRTD7\Xia 等 - 2026 - FastMMoE accelerating multimodal large language models through dynamic expert activation and routin.pdf"]

要求：
1. 严格按照 `paper-reading` 的 6 个一级部分输出：
   - Task
   - Challenge
   - Inspiration
   - Insight & Novelty
   - Potential Flaw
   - Motivation
2. 省略所有客套话，直接进入内容。
3. 使用中文输出。
4. 使用 Markdown 输出。
5. 可以使用**少量必要公式**帮助说明任务定义、目标函数或关键机制，但要遵守以下约束：
   - 只在确实能帮助理解时使用，不要为了显得“学术”而堆公式
   - 优先使用简洁的纯文本公式
   - 如果确实需要 LaTeX，也只允许非常短的小公式，不要出现大段公式推导
   - 公式的作用是澄清问题，而不是替代文字解释
6. 对 Task 尽可能形式化描述，明确 input、output、objective、constraints、goal。
7. 对 Inspiration 区分：
   - 显式启发：作者明确说出来的启发
   - 隐式启发：你根据问题和方法匹配关系推断出的启发
8. 对每一个 Novelty，严格使用这个格式：
   [创新点解决的问题] -> [受哪个 insight 启发] -> [设计了什么创新点，具体机制是什么]
9. 如果论文中没有明确说明某个问题，请直接写：
   `论文中未明确说明`
10. 不要泛泛复述摘要，要从第一性原理出发，解释这篇论文为什么会这样设计、旧方法为什么不够、它真正的 insight 和局限分别是什么。
11. **必须把最终结果写入文件**，不要只在对话里输出。
12. 输出路径强制规定为：
    `./mystle/results/paper-reading/[paper_slug].md`
13. `paper_slug` 生成规则：
    - 优先使用 arXiv ID
    - 否则使用 PDF 文件名
    - 否则使用论文标题转成简短 slug
    - slug 只保留小写字母、数字和连字符 `-`
14. 默认直接把结果写到最终文件路径，不要为了“确认目录是否存在”额外运行 `ls`、`dir`、`mkdir`、`cd` 等 shell 命令。
15. 本仓库中的目标目录 `./mystle/results/paper-reading/` 通常已存在；只有在写文件时**明确报错提示父目录不存在**，才创建这一个目录并立即重试一次。
16. 优先使用文件读写工具完成输出，不要把“创建目录”当成单独阶段去执行或汇报。
17. 在写完文件后，请在对话里只返回：
    - 最终输出文件路径
    - 论文标题
    - 3 行以内的简短摘要