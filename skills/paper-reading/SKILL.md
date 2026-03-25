---
name: paper-reading
description: Deeply read and dissect a research paper from first principles. Use when the user says "读论文", "精读 paper", "分析这篇文章", "拆解论文", "what does this paper say", or wants a structured explanation of one paper's task, challenge, inspiration, insight, novelty, flaws, and motivation.
argument-hint: [paper-title-or-url-or-pdf-path]
allowed-tools: Bash(*), Read, Glob, Grep, WebSearch, WebFetch, Write, Agent
---

# Paper Reading

Paper to read: $ARGUMENTS

## Purpose

Use this skill to deeply read a **single paper** and explain it from a **first-principles** perspective.

The goal is not to paraphrase the abstract. The goal is to reconstruct:

- what problem the paper is really solving
- why older approaches are insufficient
- what inspired the authors
- what the actual insight is
- where the novelty truly lies
- what limitations or follow-up directions matter
- how one could naturally arrive at the paper's core idea

This skill is for **single-paper deep reading**. If the user wants a multi-paper landscape or related-work survey, use `/research-lit` instead.

## Reading Style

- Be a **first-principles thinker**: reason from the problem definition, constraints, mechanism, and evidence.
- **Skip all pleasantries**. Start directly with the content.
- Write in **clean Markdown**.
- **Do not use LaTeX** in the output.
- If formulas are needed, express them in plain text only.
- Prefer precise, structured statements over vague praise.
- If the paper does not provide enough evidence for a claim, say so explicitly.
- If a sub-question cannot be answered from the paper, state `论文中未明确说明` rather than guessing.

## Input Handling

Accept any of the following:

- paper title
- arXiv ID or URL
- publisher URL
- local PDF path
- local markdown / note file summarizing a paper

If the input is ambiguous:

1. identify the paper first
2. fetch or read the most authoritative available source
3. prefer the paper itself over blog posts or third-party summaries

When reading a paper, prioritize these sections:

1. title + abstract
2. introduction
3. method / approach
4. experiments / results
5. conclusion
6. appendix only if needed to clarify a key mechanism or assumption

## Analysis Workflow

### Step 1: Reconstruct the Task

Answer:

- what is the input?
- what is the output?
- what objective is being optimized?
- what constraints or assumptions define the setting?
- what would count as success or failure?

Whenever possible, restate the task in a more formal way using plain text:

```text
Given: ...
Predict / optimize: ...
Subject to: ...
Goal: ...
```

### Step 2: Reconstruct the Challenge

Explain why the task is hard.

Look for:

- information bottlenecks
- optimization difficulty
- data scarcity or noise
- distribution shift
- combinatorial explosion
- multi-objective tradeoffs
- memory / compute / latency constraints
- mismatch between training objective and real goal

Then explain what traditional methods did and where they break.

### Step 3: Identify Inspiration

Before naming the paper's insight, identify where the idea seems to come from.

Possible inspiration sources:

- an empirical phenomenon
- a failure mode in prior work
- an analogy to another field
- a decomposition of the task into easier subproblems
- a systems constraint that forces a design choice
- a representation bottleneck
- a scaling observation

Do not invent inspiration if the paper does not support it. Separate:

- **explicit inspiration**: directly stated by the authors
- **implicit inspiration**: can be inferred from the problem-method fit

### Step 4: Extract Insight And Novelty

For each major insight:

1. state the insight in one sentence
2. explain what aspect it is about:
   - representation
   - architecture
   - objective
   - optimization
   - data construction
   - inference strategy
   - systems design
3. connect it to the relevant inspiration
4. identify the concrete novelty that implements the insight

For each novelty, use this strict format:

```text
[创新点解决的问题] -> [受哪个 insight 启发] -> [设计了什么创新点，具体机制是什么]
```

Treat novelty carefully:

- distinguish **core novelty** from engineering details
- distinguish **architecture novelty** from **training strategy novelty**
- distinguish **method novelty** from **problem framing novelty**
- if the paper mainly recombines known components, say so honestly

### Step 5: Evaluate Potential Flaws

Check the paper from three angles.

1. **Scenario limitation**
   - Is the task setting too narrow?
   - Would the method likely break under more dimensions, more conditions, more constraints, or more realistic deployment assumptions?

2. **Data pathology**
   - What bad data properties would make the method struggle?
   - Examples: long-tail labels, noisy supervision, missing modalities, spurious correlations, non-stationarity, heavy class imbalance, sparse reward, low-resource regime.

3. **Research opportunity**
   - Among the above limitations, which one is most worth turning into a new paper?
   - Prefer directions where the limitation is structural, important, and testable.

### Step 6: Reconstruct The Motivation

Summarize how one could naturally arrive at the paper's idea from first principles.

Prefer a short chain of question-driven reasoning, for example:

```text
之前的方法把问题当成 ... 来做，但真正的瓶颈其实是 ...
那能不能先把 ... 分解出来？
如果真正困难在于 ...，那是不是应该把建模重点放到 ... 上？
既然 ... 是主要误差来源，那么能不能设计一种机制专门处理 ...？
```

This section should feel like the **most natural path to the idea**, not a retrospective glorification.

## Output Format

Always use exactly these 6 top-level sections:

```markdown
## 1. Task

- ...

## 2. Challenge

- ...

## 3. Inspiration

### 3.1 Explicit Inspiration
- ...

### 3.2 Implicit Inspiration
- ...

## 4. Insight & Novelty

### 4.1 Core Insights
- Insight 1: ...
- Insight 2: ...

### 4.2 Novelty Mapping
- [创新点解决的问题] -> [受哪个 insight 启发] -> [设计了什么创新点，具体机制是什么]
- ...

## 5. Potential Flaw

### 5.1 Scenario Limitation
- ...

### 5.2 Data Pathology
- ...

### 5.3 Best Follow-up Paper Direction
- ...

## 6. Motivation

- ...
```

## Quality Bar

- The `Task` section should be formal enough that a reader could almost implement the problem setup.
- The `Challenge` section should explain why naive approaches fail, not just list buzzwords.
- The `Inspiration` section should distinguish observed inspiration from your inference.
- The `Insight & Novelty` section should map from insight to mechanism, not stop at slogans.
- The `Potential Flaw` section should include at least one serious limitation, not a fake weakness.
- The `Motivation` section should read like a natural reasoning path, ideally phrased as a sequence of sharp questions.

## File Output

- If the caller specifies an output file, write directly to that final path.
- Do not run shell commands just to check whether the parent directory exists.
- Only create the parent directory if the file write explicitly fails because the directory is missing, then retry once.
- Prefer file read/write tools over shell-based file operations.

## When To Push Back

Push back gently if:

- the input is not actually a paper
- only a tweet / blog summary is provided but the user asks for deep analysis
- the paper is too long or the source is incomplete and the missing sections matter

In those cases, say what is missing and continue with the strongest analysis possible from available evidence.

## Composition

Useful follow-ups:

```text
/paper-reading "paper url or pdf"   -> deep single-paper analysis
/research-lit "topic"               -> related work and literature landscape
/novelty-check "idea"               -> compare a new idea against existing papers
/research-review "draft or idea"    -> external critical review of your own work
```
