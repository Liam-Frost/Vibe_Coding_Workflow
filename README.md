# Vibe Coding Workflow

This repository provides an **engineering-grade workflow** for building and maintaining **long-running, complex software projects with AI assistance**.

It is **not** a prompt collection, a coding assistant, or a productivity hack.

It is a **process and structure** for preventing AI-assisted projects from collapsing under context loss, hidden assumptions, and execution drift.

---

## The Problem This Project Solves

When using AI in real software projects, teams commonly encounter:

- Context loss as conversations grow longer or become sluggish
- Inconsistent behavior when starting a new chat window
- AI silently filling in missing requirements with assumptions
- Misalignment between “discussion AI” and “execution AI”
- Human manual edits breaking AI’s understanding of the codebase
- No reliable way to resume work after time gaps or model switches

These failures are not caused by “bad prompts.”

They are caused by **treating AI as a memory-based chat agent instead of an engineering collaborator**.

---

## Core Idea

> **Conversation context is perishable.  
> Engineering memory must live in the repository.**

This workflow treats:
- Chat windows as disposable
- Repository files as the single source of truth
- AI as a stateless collaborator that must be cold-started correctly every time

---

## What This Workflow Enforces

- **Cold-startable collaboration**  
  Any new AI conversation can be brought into sync using a small, explicit context snapshot.

- **Strict separation of planning and execution**  
  Planning AI clarifies scope, surfaces gaps, and freezes decisions.  
  Execution AI (e.g. Codex) only reads the repository and submits PRs.

- **No implicit decisions**  
  Unknowns are marked as TBD.  
  Conflicts and trade-offs are surfaced explicitly before implementation.

- **Auditable iterations**  
  Every iteration has:
  - An intent (Iteration Brief)
  - A gap analysis (Gap Scan)
  - A frozen execution boundary (Implementation Contract)
  - Verifiable output (PRs + acceptance criteria)

- **Model-agnostic design**  
  The workflow does not depend on any specific AI model’s memory or behavior.

---

## What This Repository Contains

- `WORKFLOW.md`  
  The full, step-by-step operating manual explaining the workflow in detail.

- A reference repository structure for:
  - Engineering memory (`AI_CONTEXT`, contracts, decisions)
  - Iteration management
  - Acceptance and smoke validation

- Prompt templates for:
  - Conversation bootstrapping
  - Iteration planning
  - Gap scanning
  - Contract freezing
  - Codex execution (Chinese + English)

---

## Who This Is For

This workflow is designed for:

- Engineers building **non-trivial, evolving software systems**
- Solo developers using AI over long time horizons
- Teams who want AI assistance **without losing architectural control**
- Anyone frustrated by “the AI forgot what we decided last week”

It is **not** optimized for:
- One-off scripts
- Throwaway prototypes
- Prompt-only experimentation

---

## How to Use This Repository

1. Read `WORKFLOW.md` end-to-end  
   This is a process document, not a reference sheet.

2. Use this repo as a **template** for new projects  
   Copy the structure and adapt it to your codebase.

3. Treat all AI collaboration as **stateless by default**  
   Always cold-start new conversations using the provided Bootstrap Prompt.

---

## Philosophy (One Sentence)

> **AI should assist engineering decisions, not silently make them.**

---

# Vibe Coding 指北

本仓库提供一套**工程级的软件开发工作流**，用于在**长期、复杂的软件项目中稳定地使用 AI 协作**。

它不是 Prompt 集合，不是“AI 写代码技巧”，也不是效率工具。

它是一套**系统性的方法论**，用于解决 AI 协同开发中反复出现、但很少被正面处理的结构性问题。

---

## 这个项目解决什么问题？

在真实项目中使用 AI，常见问题包括：

- 聊天窗口变长后，AI 开始遗忘或混淆早期结论
- 新开窗口后，AI 对项目状态一无所知
- 在需求不完整时，AI 自动补全假设
- 讨论阶段的 AI 与执行阶段的 AI 认知不一致
- 人类手动修改代码，导致 AI 的理解彻底失效
- 项目中断一段时间后，几乎无法安全恢复上下文

这些问题并不是“不会写 Prompt”。

而是**把 AI 当作有记忆的聊天对象，而不是工程协作者**所导致的。

---

## 核心理念

> **聊天上下文是易腐品，工程记忆必须落在仓库中。**

本工作流的基本假设是：

- 聊天窗口随时可能失效
- AI 不应被假设“记得之前的事情”
- 唯一可信的长期记忆是工程文件

---

## 这套工作流强制解决的事情

- **可冷启动的 AI 协作**  
  任意新聊天窗口，都可以通过极少量文件快速同步。

- **规划与执行严格分离**  
  讨论端 AI 负责澄清、扫描缺口、冻结决策；  
  执行端 AI 只读仓库、改代码、提 PR。

- **拒绝隐式决策**  
  所有未知必须显式标记 TBD，所有取舍必须在实现前确认。

- **可审计的工程迭代**  
  每一轮迭代都有明确意图、边界、验收标准和回写机制。

- **模型无关**  
  不依赖任何 AI 的“记忆能力”或上下文长度。

---

## 仓库包含内容

- `WORKFLOW.md`  
  完整的工程级操作说明书。

- 一套可复用的项目目录结构，用于：
  - 工程记忆
  - 迭代管理
  - 验收与回归

- 多种 Prompt 模板，用于：
  - 新窗口冷启动
  - 迭代规划
  - Gap Scan
  - 执行边界冻结
  - Codex 执行（中英文）

---

## 适合谁使用？

- 构建**中大型、持续演进系统**的工程师
- 长期使用 AI 的独立开发者
- 希望在 AI 协助下**仍然保持架构控制权**的团队
- 被“AI 忘了我们之前决定什么”反复折磨的人

不适合：

- 一次性脚本
- 快速丢弃的原型
- 只追求短期效率的 Prompt 实验

---

## 使用方式

1. 从头到尾阅读 `WORKFLOW.md`
2. 将本仓库作为新项目的模板
3. 把所有 AI 协作当作“默认无记忆”的冷启动过程

---

## 一句话哲学

> **AI 应该辅助工程决策，而不是悄悄替你做决定。**
