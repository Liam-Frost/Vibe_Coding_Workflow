# AI-Assisted Software Engineering Workflow (WORKFLOW)

> This document is an **engineering-grade operating manual**.
> The goal is not to teach “how to code with AI,” but to define a workflow that enables **stable, reliable collaboration with AI on long-running, complex software projects**, systematically addressing context loss (context rotting), implicit assumptions, implementation conflicts, and broken engineering memory.

---

## 1. What problems does this workflow solve?

In real-world AI-assisted projects, common failure modes include:

- When a conversation window becomes long or slow, the AI starts **forgetting or distorting earlier information**
- When you open a new conversation window, the AI **doesn’t know the project state, collaboration rules, or the current phase**
- When requirements are incomplete, the AI **auto-fills assumptions**, introducing hidden decisions
- The planning AI and the execution AI (Codex) become **misaligned**
- Human manual edits bypass AI, causing future AI guidance and the codebase to **diverge badly**

The core objective of this workflow:

> **Transform AI from a “context-driven chat partner” into a “collaborator driven by engineering files.”**

---

## 2. Core principles (must understand)

### 2.1 Repository files are the single source of truth (Single Source of Truth)

- All engineering facts, constraints, and decisions must be written into repository files
- Chat content does not constitute long-term memory
- If something exists only inside a conversation window, it effectively does not exist

---

### 2.2 Conversation windows are perishable (context rotting is normal)

- Every conversation window has an effective context limit
- The longer the context, the more likely you’ll see forgetting, confusion, repetition, and performance degradation
- When these symptoms appear, continuing in the same window becomes **high risk**

**Conclusion:**

> Treat conversation windows as disposable tools. Discard and open a new window as a normal operation.

---

### 2.3 Strict separation of planning and execution

| Role | Responsibility |
|---|---|
| Planning-side AI (e.g., GPT / Claude) | Clarify requirements, surface gaps, freeze phase decisions, produce execution instructions |
| Execution-side AI (Codex) | Read the repo, modify code, submit PRs |

The planning AI **does not directly change code**, and the execution AI **does not participate in discussion or decision-making**.

---

### 2.4 AI must not make implicit decisions

- Unconfirmed information must be labeled as TBD
- Conflicts and trade-offs must be made explicit
- AI must not “quietly decide for you” during implementation

---

### 2.5 The workflow must be model-agnostic

- Any planning-side AI can be swapped in as long as it follows the prompts
- Do not rely on model “memory” or “context retention” capabilities

---

### 2.6 Language Consistency Rule

This repository contains bilingual (English + Chinese) templates.

For any given project:
- Exactly **one language** must be chosen as the active language.
- The other language must be treated as comments and ignored.
- Mixing languages within the same project or file is prohibited.

All AI collaborators and human contributors must respect the
**Language Usage Notice** at the top of each bilingual file.

---

## 3. Key concept clarifications (very important)

### 3.1 Engineering iteration (Iteration)

**An iteration is an engineering unit, not a chat unit.**

An iteration is defined as:

> From proposing an engineering goal
> to delivering implementation (possibly across multiple PRs) and updating engineering memory

If the engineering goal is not finished, you are still in the same iteration.

---

### 3.2 Conversation window (Conversation Session)

A conversation window is a **continuous context interaction environment** between you and the AI, with these characteristics:

- It has a context length limit
- Context rotting occurs
- It is not suitable as an engineering memory store

**Critical rule:**

> Whenever you open a new conversation window for any reason (too long, laggy, forgetting, model switch, etc.), treat it as a “cold start.”

---

### 3.3 Cold-start sync (Hard Rule)

**At the start of every new conversation window, you must sync the following:**

1. Conversation Bootstrap Prompt (declares the workflow and collaboration rules)
2. `AI_CONTEXT.md` (the project’s current factual snapshot)
3. The current iteration’s Iteration Brief (if you are already inside an iteration)
4. Current phase artifacts (e.g., existing Gap Scan / Contract)

This is mandatory and must not be skipped.

---

### 3.4 Execution (Codex)

Codex is an execution tool, not an engineering phase.

- An iteration may have zero Codex usage
- Or multiple Codex PRs
- Whether to split PRs depends on risk and scope, not on workflow rules

---

### 3.5 Iteration Brief vs AI_CONTEXT

- **AI_CONTEXT.md**: “What the project is right now” (factual snapshot, used for cold-starting new windows)
- **Iteration Brief**: “What this iteration is trying to do” (intent and boundaries)

They serve different purposes; both are required.

---

## 4. Template repository structure

### 4.1 Full project structure (structure only)

```
/
├─ WORKFLOW.md
├─ README.md
│
├─ docs/
│   ├─ governance/
│   │   ├─ PROJECT\_CONTRACT.md
│   │   ├─ AI\_CONTEXT.md
│   │   ├─ DECISION\_INDEX.md
│   │
│   ├─ spec/
│   │   ├─ DESIGN\_FREEZE.md
│   │   ├─ REQUIREMENTS.md
│   │   └─ ACCEPTANCE\_TESTS.md
│   │
│   ├─ adr/
│   │   └─ ADR-0001-template.md
│   │
│   └─ iterations/
│        └─ YYYY-MM-DD-.md
│
├─ src/
└─ tests/
    └─ smoke-checklist.md
```

---

### 4.2 Directory responsibilities (boundary of duties)

- `/`: project entry layer
- `docs/`: engineering memory layer shared across windows, people, and AIs
- `docs/governance/`: hard constraints and factual snapshots
- `docs/spec/`: requirements and acceptance criteria
- `docs/adr/`: architecture decision records
- `docs/iterations/`: per-iteration intent anchors
- `src/`: implementation layer
- `tests/`: minimal validation and regression assets

---

### 4.3 File purposes (positioning and usage)

- `WORKFLOW.md`: this manual, defines process and rules
- `README.md`: entry doc (how to run, what it does, what it does not do)
- `PROJECT_CONTRACT.md`: project charter (hard constraints/invariants/forbidden items)
- `AI_CONTEXT.md`: AI cold-start factual snapshot
- `DECISION_INDEX.md`: decision index (conclusion-level)
- `DESIGN_FREEZE.md`: bootstrap design freeze
- `REQUIREMENTS.md`: requirements spec
- `ACCEPTANCE_TESTS.md`: acceptance criteria
- `ADR-0001-template.md`: ADR template
- `docs/iterations/*.md`: iteration briefs
- `tests/smoke-checklist.md`: minimal smoke/regression checklist

---

## 5. Conversation Bootstrap Prompt (window-level mandatory entry)

**This is the single most important mechanism in the entire workflow.**

### 5.1 Definition

A Conversation Bootstrap Prompt is:

> **The first message in every new conversation window**
> used to declare the workflow, collaboration rules, sources of truth, and the current engineering phase.

It replaces any approach that “assumes the AI remembers the rules.”

---

### 5.2 When to use (mandatory)

- New project bootstrap
- Opening a new window within the same project
- When a window shows signs of context rotting
- Switching the planning-side AI model

---

### 5.3 Purpose

- Immediately puts the AI into “engineering collaboration mode”
- Reduces uncertainty from model differences
- Ensures consistency across windows

(Template in §10.1)

---

## 6. Project bootstrap phase (Project Bootstrap)

### 6.1 Bootstrap goals

The bootstrap phase does not aim to ship features. The goals are:

- Clarify project boundaries
- Freeze the initial design
- Build cold-startable engineering memory

---

### 6.2 Bootstrap steps

#### Step 1: Open a new conversation window

Use the Conversation Bootstrap Prompt (auto-detects “project bootstrap” vs “project continuation”) (see §10.1)

Then start describing the project details.

#### Step 2: Project design discussion (exploration)

- Goals
- Non-goals
- Technical direction
- Key risks
- TBDs

**The output of this phase is not code, but raw material for DESIGN_FREEZE.md.**

(Optional) If you haven’t pinned down boundaries yet, you can do multiple exploration rounds after Bootstrap until you can write a freeze doc.

#### Step 3: Generate and confirm `DESIGN_FREEZE.md`

This marks the end of bootstrap.

When you believe the discussion covers the core, ask the planning AI to draft `docs/spec/DESIGN_FREEZE.md`, then you review and confirm.

The purpose of freezing: prevent the AI from silently introducing new assumptions when generating the initial doc pack.

#### Step 4: Generate the initial doc pack (planning AI fills templates; you review)

Based on the confirmed DESIGN_FREEZE.md, ask the planning AI to draft the following files. You review, cut, confirm, and commit:

- `README.md`
- `docs/governance/PROJECT_CONTRACT.md`
- `docs/governance/AI_CONTEXT.md` (initial)
- `docs/governance/DECISION_INDEX.md` (initial)
- `docs/spec/REQUIREMENTS.md` (v0)
- `docs/spec/ACCEPTANCE_TESTS.md` (v0)
- `tests/smoke-checklist.md` (v0)
- `docs/adr/ADR-0001-template.md` (if not present)

> The most important bootstrap outcome is not “how much you wrote,” but:
> 
> 1) AI_CONTEXT supports cold-starting any new window; 2) Contract defines hard boundaries;
> 2) Requirements + Acceptance define “done”; 4) Decision Index + ADR system can carry future decisions.

### 6.3 Bootstrap Todo Checklist

- [ ] Planning AI has aligned to the workflow (§10.1)
- [ ] DESIGN_FREEZE.md is confirmed (with a TBD list)
- [ ] Initial doc pack is generated and reviewed/committed
- [ ] AI_CONTEXT.md can cold-start any new window (short, copyable)
- [ ] REQUIREMENTS and ACCEPTANCE_TESTS cover at least v0 goals and validation criteria

---

## 7. Iteration execution phase (Iteration Loop)

### 7.1 Standard structure of each iteration

1. **Iteration Brief (v0)**: start rough; perfection is not required
2. **Clarification (discussion)**: close key gaps around the brief
3. **Gap Scan**: planning AI outputs gaps and conflicts
4. **Implementation Contract**: freeze decisions and boundaries (you confirm)
5. **Codex Prompt**: planning AI generates execution instructions
6. **Codex execution**: one or multiple PRs
7. **Review & Merge**: you gatekeep
8. **Doc Delta (write-back)**: update AI_CONTEXT / Decision Index etc.

---

### 7.2 Step 1: Iteration Brief (can be rough)

**Purpose**: Turn “ideas/pain points” into an iteration intent anchor and prevent discussion drift.

A rough Iteration Brief means you do not need to know up front:

- Exactly which files will change
- Precise acceptance criteria
- The final implementation design

Recommended location: `docs/iterations/YYYY-MM-DD-<topic>.md`

If you’re not sure what to do this iteration, use the appendix template **§10.2 (Iteration Brief v0)** to help the planning AI structure your fuzzy intent.

---

### 7.3 Step 2: Clarification discussion (can span multiple windows)

**Purpose**: Compress uncertainty to the point where you can run a Gap Scan and write a Contract.

Discussion topics include:

- Whether the goal should be split into multiple iterations
- Which unknowns must be resolved first (otherwise the route changes)
- Which hard constraints must be explicitly written
- Which modules/files might be touched (rough is fine)

> You do not need a final solution here; you need to make the unknowns explicit.

---

### 7.4 Step 3: Gap Scan (one concentrated pass)

**Purpose**: Before “freezing decisions,” surface gaps and conflicts so the execution AI won’t invent assumptions.

The position of Gap Scan is fixed: **before Implementation Contract**.

The planning AI must output:

- Missing Info (what would change the implementation route)
- Conflicts / Trade-offs
- Refuse-to-assume (assumptions it refuses to auto-fill)
- Questions (items you must confirm)
- Risk Notes (sources of bugs/redundancy/conflicts)

Use appendix template: **§10.3 Gap Scan Prompt**.

> Usually once per iteration is enough; if the direction changes substantially mid-iteration, run it again.

---

### 7.5 Step 4: Implementation Contract (freeze)

**Purpose**: Turn discussion into executable, auditable boundaries: what you do, what you don’t do, what you must not do, and what remains undecided.

The Contract is the iteration’s “temporary charter.” It:

- Freezes scope into executable items
- Explicitly lists non-goals (prevents scope creep)
- Explicitly references hard constraints (prevents accidental violations)
- Fixes the TBD list (prevents silent completion)
- Provides testable acceptance criteria (prevents “looks good” ambiguity)

Use appendix template: **§10.4 Implementation Contract Prompt**.
You must confirm the Contract (Confirmed) before moving to Codex Prompt.

---

### 7.6 Step 5: Codex Prompt (execution instructions)

**Purpose**: Convert a confirmed Contract into instructions Codex can execute directly: what to read, what to change, what must not change, how to self-check, how to write PR description.

A Codex prompt must include:

- Read-first: which repo files to read first (AI_CONTEXT, brief, source files)
- File touch list: planned modified files
- Tasks: prioritized task list
- Invariants: hard constraints not to violate (from PROJECT_CONTRACT / AI_CONTEXT)
- Self-check: regression and validation (smoke + acceptance)
- TBD handling: how to handle TBDs (report in PR; no unilateral decisions)

Use appendix template: **§10.5 Codex Prompt Template (Chinese)**.

---

### 7.7 Step 6: Codex execution and PRs

**Purpose**: Use PR boundaries to make execution auditable and deliverable.

Multiple Codex PRs are allowed. Common pattern:

- PR-1: minimal runnable skeleton (MVP)
- PR-2: edge cases and regression
- PR-3: refactor/cleanup (if needed)

PR split decision:

- High risk / large scope / wide impact: split is safer
- Small changes: a single PR is fine

---

### 7.8 Step 7: Review & Merge

**Purpose**: Ensure hard constraints aren’t broken, hidden decisions aren’t introduced, and acceptance criteria are genuinely met.

You should check:

- Violations of PROJECT_CONTRACT
- New assumptions introduced without being declared
- Data model or API changes without doc updates
- Acceptance criteria and smoke checks

---

### 7.9 Step 8: Engineering memory write-back (Doc Delta)

**Purpose**: Maintain cold-start consistency so any future person/AI can recover the real state from the repo.

After merge, do a “minimal write-back,” centered on cold-start correctness.

Recommended doc update tiers:

- **Every iteration (lightweight)**
  - AI_CONTEXT.md: Recent Changes, Active TBD (if changed)
- **Conditional**
  - DECISION_INDEX.md: if decisions were added/closed or TBD status changed
  - REQUIREMENTS / ACCEPTANCE_TESTS: if scope changed or new acceptance items were added
- **Rare**
  - PROJECT_CONTRACT: only if invariants/forbidden items changed

---

### 7.10 Iteration Todo Checklist

- [ ] Iteration Brief (v0 or better) created and committed under `docs/iterations/`
- [ ] If a new conversation window was opened: Bootstrap Prompt + AI_CONTEXT + brief + existing artifacts (Gap Scan/Contract/Prompt) were synced
- [ ] Clarification is done (key unknowns confirmed)
- [ ] Gap Scan completed and addressed (gaps/conflicts/refused assumptions)
- [ ] Implementation Contract confirmed
- [ ] Codex submitted PRs per prompt (multiple PRs allowed)
- [ ] PRs reviewed and merged; acceptance + smoke satisfied
- [ ] Doc Delta completed (at least AI_CONTEXT updated)

---

## 8. How to include manual edits in the workflow

This workflow does not require all changes to go through AI. The key is to incorporate manual edits into engineering memory.

### 8.1 Change categories

- Trivial (no semantic impact): direct edit is fine
- Local semantic (local behavior change): prefer PR + short explanation
- Structural (architecture/decision-level): must follow iteration process (brief + contract)

### 8.2 Minimum requirements for manual edits

- Must not violate PROJECT_CONTRACT
- PR description or commit message must state:
  - Whether decisions/assumptions were affected
  - Whether AI_CONTEXT / Decision Index needs updates

---

## 9. One-sentence summary

> **Conversation windows are perishable; engineering memory must live in the repo.**
> **Every new window must sync: Bootstrap Prompt, AI_CONTEXT, Iteration Brief.**
> **Iterations are engineering loops; Codex is an execution tool.**

---

## 10. Appendix: Prompt templates

> This section provides copyable prompt templates for any planning-side AI.
> Templates assume you have already performed the required cold-start sync at the beginning of the window (see §3.3), so they do not include placeholder instructions like “paste file X here.”

---

### 10.1 Conversation Bootstrap Prompt

```
You are the planning-side AI (not the execution-side AI).
You assist with requirement clarification, gap scanning,
decision freezing, and generating Codex execution instructions.
You must strictly follow the workflow and collaboration rules below.

[0. Role and Boundaries]
- You are responsible for:
  - clarifying requirements
  - surfacing conflicts and gaps
  - making constraints and assumptions explicit
  - filling structured engineering artifacts ONLY when explicitly authorized
    and ONLY after the corresponding template has been provided
    (DESIGN_FREEZE / Gap Scan / Implementation Contract / Codex Prompt).
- You are NOT responsible for:
  - editing code directly
  - making unauthorized engineering decisions
  - auto-filling missing requirements, assumptions, or trade-offs
  - advancing the engineering stage on your own
- Execution is handled by Codex.
  Your deliverables are high-confidence instructions suitable for Codex,
  not implementations.

[1. Source of Truth (Single Source of Truth)]
- Repository files are the single source of truth.
- Chat content is not long-term memory.
- If repository content conflicts with chat content, follow the repository.
- Uncertain items must be labeled as TBD.
- You must not invent or infer missing information.

[2. Conversation Window Characteristics (Cold Start)]
- This is a new conversation window.
- You must assume you cannot reliably remember anything
  that is not explicitly synced in this window.
- If required information is missing, you must request it instead of guessing.

[3. Language Consistency (Mandatory)]
- This repository uses bilingual templates (English + Chinese).
- You must detect and strictly follow the “Language Usage Notice”
  at the top of each file.
- Exactly ONE language is active per project.
- All of your outputs must use the active language only.
- Translating, mirroring, or mixing the inactive language is strictly prohibited.

[4. Automatic Mode Detection (Detection Only, No Advancement)]

You must detect which of the following three modes the current conversation
window is in.

You may ONLY detect and constrain your own behavior.
You must NOT advance the engineering workflow, switch stages,
or assume progression on your own.

All stage transitions must be explicitly confirmed by me.

────────────────
Mode A | Project Bootstrap (Greenfield)
────────────────
Trigger conditions:
- AI_CONTEXT.md has NOT been provided, AND
- I explicitly state that this is a new project or a from-scratch bootstrap window

In Mode A, your responsibilities are STRICTLY LIMITED to the following:

A1) Project Boundary & Design Intent Clarification
- You may ONLY use discussion to:
  - clarify project goals, non-goals, constraints, preferences, risks, and TBDs
- In this sub-stage:
  - You must NOT generate any repository file content
  - You must NOT assume the project is ready for DESIGN_FREEZE
- You MAY suggest:
  - “If you want to enter the DESIGN_FREEZE Draft stage,
     please paste the DESIGN_FREEZE.md template.”

A2) DESIGN_FREEZE.md (Draft Stage)
- Entry conditions (ALL must be met):
  - I explicitly request generation of a DESIGN_FREEZE Draft
  - I have pasted the DESIGN_FREEZE.md template
- In this sub-stage:
  - You may generate the DESIGN_FREEZE **Draft only**
  - You must NOT fill in:
    - confirmation status
    - confirmed-by fields
    - confirmation dates
- After producing the Draft:
  - You must wait for my review and feedback
  - You must NOT assume the Draft has been accepted or frozen

A3) DESIGN_FREEZE.md (Confirmed Transition)
- ONLY when I explicitly state that:
  - “The DESIGN_FREEZE Draft is confirmed”
- You may then:
  - instruct me to copy the Confirmed version into the repository
  - instruct me to manually fill in the confirmer name and confirmation date
- Until this explicit confirmation:
  - You must NOT proceed to the initial document pack stage

A4) Initial Document Package Generation (Strict Order, One File at a Time)

* **Prerequisites (all must be satisfied):**

  * `DESIGN_FREEZE.md` has been explicitly confirmed by me
  * The **Confirmed** version has been committed to the repository

* The initial document package **must be generated sequentially in the following fixed order**.
  Skipping, parallel generation, or reordering is strictly prohibited:

  1. `README.md`
  2. `PROJECT_CONTRACT.md`
  3. `AI_CONTEXT.md` (initial version)
  4. `DECISION_INDEX.md` (initial version)
  5. `REQUIREMENTS.md` (v0)
  6. `ACCEPTANCE_TESTS.md` (v0)
  7. `smoke-checklist.md` (v0)
  8. `ADR-0001-template.md` (template only; **do not generate any ADR instance**)

* During this phase, you must strictly adhere to the following rules:

  * You must **not** generate more than one document at a time

  * Each document must independently go through the following complete loop:

    1. Explicitly state **which document will be generated next**
    2. Briefly explain the role of this document in the project bootstrap phase (1–2 sentences)
    3. Require me to paste the corresponding template
    4. Generate a **Draft** only after the template is provided
    5. Wait for my review and feedback
    6. After I confirm the draft, instruct me to copy the **Confirmed** version into the repository
       (the confirmer name and confirmation date must be filled in **manually by me**)

  * You must **pause the process and alert me** if any of the following occurs:

    * The required template has not been provided
    * I attempt to skip or reorder the sequence
    * The generated content would introduce assumptions outside of `DESIGN_FREEZE.md`

* **Only when all documents above have completed the full cycle**
  (Draft → Review → Confirmed)

* You may then:

  * Indicate that the **project bootstrap phase is complete**
  * Ask whether I would like to consider entering the next engineering phase
    (**you must not transition phases on your own**)

---

If you want, next we can:

* Inline this cleanly into the **full English Bootstrap Prompt**
* Or normalize **A1–A4** into a formal **state-machine style spec**
* Or add a short **“Why ADRs are forbidden during bootstrap”** rationale block (English + Chinese) for WORKFLOW.md

────────────────
Mode B | Project Continuation
────────────────
Trigger condition:
- I have provided docs/governance/AI_CONTEXT.md

In Mode B:
- You may ONLY work toward the explicitly declared “window goal”
- You must strictly follow:
  - stage-gating rules
  - template-dependency rules
- You must NOT:
  - automatically enter Gap Scan, Implementation Contract, or Codex Prompt stages
  - generate any stage artifact without the required template
- If you detect:
  - missing information
  - incomplete context
  - a mismatch between AI_CONTEXT.md and the current goal
- You must pause progression and suggest that Mode C may be required

────────────────
Mode C | Exception / Incomplete Context (Recovery Mode)
────────────────
Trigger conditions (ANY of the following):
- The provided information is insufficient to clearly apply Mode A or Mode B
- AI_CONTEXT.md is clearly incompatible with the current discussion goal
- The project is in a non-standard state
  (e.g., interruption, refactor-in-progress, migration, rollback)
- I explicitly state that this is an exception or recovery scenario

In Mode C:
- You must NOT generate any repository file content
- You must NOT enter any freeze or execution-related stage
- Your responsibilities are STRICTLY LIMITED to:
  - identifying where uncertainty or inconsistency exists
  - listing concrete reasons blocking entry into Mode A or Mode B
  - specifying exactly what information I need to provide or clarify
- ONLY when:
  - I explicitly confirm that the situation is clarified
  - AND I instruct you to switch to Mode A or Mode B
- May you exit Mode C

[5. Stage Gating (Critical Rule)]
- You must NOT decide:
  - when to generate DESIGN_FREEZE
  - when to perform a Gap Scan
  - when to generate an Implementation Contract
  - when to generate a Codex Prompt
- You may ONLY:
  - state the current stage
  - suggest that a next stage MAY be considered
  - explicitly state what inputs are required if I choose to proceed
- Entering the next stage is ALWAYS my decision.

[6. Template Dependency (Hard Requirement)]
You must REFUSE to generate any of the following
unless I have explicitly pasted the corresponding template:

- DESIGN_FREEZE.md (Draft or Confirmed)
- Any initial document pack file
- Gap Scan
- Implementation Contract
- Codex Prompt

If a required template is missing, you may ONLY:
- ask me to paste the template, OR
- continue discussion at the current stage

[7. Draft vs Confirmed (Mandatory Distinction)]
- All freeze-level artifacts must follow this lifecycle:
  Draft → User Review → Confirmed
- In the Draft stage:
  - You must NOT fill in “Confirmed by”
  - You must NOT fill in confirmation dates
- After I confirm a Draft:
  - You must instruct me to copy the Confirmed version into the repository
  - I must manually fill in confirmation metadata

[8. Window Goal (Exactly One Objective)]
You must not generate any stage artifact
until I explicitly declare the window goal.

Valid window goals are strictly limited to:
A) Clarification discussion (no file generation)
B) Generate or refine Iteration Brief (template required)
C) Produce Gap Scan (template required)
D) Produce Implementation Contract (template required)
E) Generate Codex Prompt (template required AND Contract confirmed)

If information is insufficient:
- Ask the minimum number of clarification questions (default ≤10)
- Explicitly state the engineering risk caused by each missing answer
- Do NOT auto-fill or assume.

[9. Output Format (Mandatory)]
Every response must begin with the following three sections:
1) Status Echo:
   - detected mode
   - current stage
   - whether conditions are met to proceed to a next stage
2) Assumptions & TBD:
   - all assumptions and unresolved items (or “None”)
3) Next Step (User-Controlled):
   - suggested options for what I may do next
   - required inputs for each option

[10. Start]
First, reply with:
“Engineering collaboration mode enabled.”

Then:
- If Mode A: ask me to describe the project (do NOT generate any files)
- If Mode B: ask me to provide AI_CONTEXT.md,
  then wait for me to declare the window goal
```

---

### 10.2 Generate Iteration Brief (use when you are not sure what to do this iteration)

```text
Please help me generate an “Iteration Brief v0” (it can be rough), and list 5–10 clarification questions I must answer.

Rules:
- The Brief is not a construction blueprint; it only needs to define the exploration boundary.
- Do not invent requirements; mark uncertain items as TBD.
- Output must include, in order:
  1) Background
  2) Frozen Goals
  3) Non-goals
  4) Known Constraints
  5) Unknowns / TBD
  6) Acceptance (draft)

My current idea/pain point:
(Describe it in natural language, even if it is only one sentence.)
```

---

### 10.3 Gap Scan Prompt (one concentrated scan before Contract)

```text
Please perform a Gap Scan for “this iteration.” Do not propose implementation yet.

Output must include the following sections:
1) Missing Info (missing items that would change the implementation route)
2) Conflicts / Trade-offs
3) Refuse-to-assume (assumptions you refuse to auto-fill)
4) Questions for me (items I must confirm)
5) Risk Notes (sources of bugs/redundancy/conflicts)
```

---

### 10.4 Implementation Contract Prompt (freeze before execution)

```text
Based on the currently synced information, output an “Implementation Contract (freeze before execution).”

Requirements:
- Do not introduce unconfirmed assumptions. If assumptions are unavoidable, put them in “Explicit Assumptions” and mark as “needs confirmation.”
- Keep TBD items; do not resolve them unilaterally.
- Acceptance criteria must be testable (preferably tied to existing acceptance items or smoke checklist).

Output format must include:
- Scope (Frozen)
- Non-goals
- Hard constraints
- Explicit assumptions (needs confirmation)
- Open questions / TBD
- Acceptance criteria
```

---

### 10.5 Codex Prompt Template (Chinese, for the execution-side)

```text
You are Codex, working on an existing repository. Execute strictly according to the instructions below.

[READ-FIRST | Mandatory before editing]
Before making any changes, you MUST read:
1) docs/governance/PROJECT_CONTRACT.md
2) docs/governance/AI_CONTEXT.md
3) The current iteration brief: docs/iterations/[FILENAME].md
4) Relevant source files (as needed): [list files explicitly]
Do not start implementation until all required documents are reviewed.

[INVARIANTS | Must not be violated]
The following constraints are absolute and must not be broken:
- [List hard invariants / forbidden actions, explicitly referencing PROJECT_CONTRACT or the Implementation Contract]

[SCOPE | Frozen tasks for this iteration]
Execute ONLY the following tasks, in priority order:
1) ...
2) ...
3) ...
Do not expand scope beyond what is listed here.

[NON-GOALS | Explicitly out of scope]
The following items must NOT be implemented or modified:
- ...

[TBD / OPEN QUESTIONS | Do NOT assume]
The following items are unresolved:
- ...
If you encounter a blocker caused by a TBD:
- Do NOT make a decision on your own.
- Mark the PR as “Blocked by TBD”.
- You may propose options in the PR description, but do not choose one.

[IMPLEMENTATION NOTES | Mandatory constraints]
- Functions / components / DOM IDs / data structures that MUST be reused:
  - ...
- Data models / storage schemas / public APIs that MUST NOT be changed:
  - ...

[SELF-CHECK | Required before submitting PR]
Before opening the PR, you must verify:
- How to run or manually validate the changes (if applicable): [describe steps]
- Smoke checklist: tests/smoke-checklist.md
- Acceptance criteria for this iteration: [reference or list items]

[PR DESCRIPTION | Must include all sections below]
Your PR description MUST contain:
1) Summary
   - A concise description of what was changed and why.
2) Constraint Check
   - Explicit confirmation that none of the INVARIANTS were violated.
3) Decision Delta
   - Did this PR introduce any new assumptions?
   - Did it add, close, or modify any TBD items?
   - Does it require updates to DECISION_INDEX.md or AI_CONTEXT.md?
4) Known Limitations / Follow-ups
   - Any known limitations, edge cases, or recommended next steps.
```




# AI 协同软件工程工作流说明（WORKFLOW）

> 本文档是一份**工程级操作说明书**。
> 目标不是介绍“如何用 AI 写代码”，而是定义一套**在长期、复杂软件项目中稳定使用 AI 的协作工作流**，系统性解决上下文丢失（context rotting）、隐式假设、实现冲突和工程记忆断裂等问题。

---

## 1. 这套工作流解决什么问题？

在真实项目中使用 AI，常见失败模式包括：

- 聊天窗口过长或变慢后，AI 开始**遗忘或扭曲早期信息**
- 新开聊天窗口后，AI **不知道项目状态、协作规则和当前阶段**
- AI 在信息不完备时**自动补全假设**，引入隐性决策
- 讨论端 AI 与执行端 AI（Codex）**认知不一致**
- 人类手动修改绕过 AI，导致后续建议与代码**严重不一致**

本工作流的核心目标是：

> **把 AI 从“上下文驱动的聊天对象”，转变为“由工程文件驱动的协作者”。**

---

## 2. 核心原则（必须理解）

### 2.1 仓库文件是唯一事实源（Single Source of Truth）

- 所有工程事实、约束、决策必须写入仓库文件
- 聊天内容不构成长期记忆
- 如果某条信息只存在于聊天窗口中，则等同不存在

---

### 2.2 聊天窗口是易腐品（Context Rotting 是常态）

- 每个聊天窗口都有上下文上限
- 上下文越长，越容易出现遗忘、混淆、重复和性能退化
- 当出现上述迹象时，**继续使用该窗口是高风险行为**

**结论：**

> 聊天窗口应被视为一次性工具，用完即可丢弃，新开窗口是正常操作。

---

### 2.3 规划与执行严格分离

| 角色 | 职责 |
|---|---|
| 讨论端 AI（如 GPT / Claude） | 澄清需求、暴露缺口、冻结阶段决策、生成执行指令 |
| 执行端 AI（Codex） | 读取仓库、修改代码、提交 PR |

讨论端 AI **不直接改代码**，执行端 AI **不参与讨论和决策**。

---

### 2.4 AI 不得隐式做决策

- 未确认的信息必须标记为 TBD
- 冲突、取舍必须显式写出
- AI 不得在实现阶段“顺手帮你决定”

---

### 2.5 工作流必须与具体模型无关

- 任意讨论端 AI 只要遵守 prompt 约定即可替换
- 不依赖模型的“记忆能力”或“上下文保留能力”

---

### 2.6 语言一致性规则

本仓库包含中英文双语模板。

对于任意一个具体项目：
- 必须且只能选择**一种语言**作为生效语言
- 另一种语言应被视为注释并完全忽略
- **禁止在同一项目或同一文件中混合使用两种语言**

所有 AI 协作者与人工贡献者都必须遵守
每个双语文件顶部的**语言使用声明（Language Usage Notice）**。

---

## 3. 关键概念澄清（非常重要）

### 3.1 工程迭代（Iteration）

**迭代是工程单位，不是聊天单位。**

一次迭代定义为：

> 从提出一个工程目标
> 到实现落地（可能包含多次 PR）并完成工程记忆更新

只要工程目标未完成，就仍处于同一个迭代。

---

### 3.2 聊天窗口（Conversation Session）

聊天窗口是你与 AI 的一次**上下文连续交互环境**，具有以下特性：

- 有上下文长度限制
- 会发生 context rotting
- 不适合作为工程记忆载体

**重要规则：**

> 当你因为任何原因（窗口太长、卡顿、遗忘、模型切换等）新开一个聊天窗口时，必须把它当作“冷启动”。

---

### 3.3 冷启动同步（Hard Rule）

**每一个新聊天窗口开始时，必须同步以下信息：**

1. Conversation Bootstrap Prompt（声明工作流与协作规则）
2. `AI_CONTEXT.md`（项目当前事实快照）
3. 当前迭代的 Iteration Brief（若已进入某轮迭代）
4. 当前工程阶段的产物（如已有 Gap Scan / Contract）

这是强制规则，不可省略。

---

### 3.4 执行（Codex）

Codex 是执行工具，不是工程阶段。

- 一次迭代可以没有 Codex
- 也可以包含多次 Codex PR
- 是否拆分 PR 取决于风险与范围，而非流程要求

---

### 3.5 Iteration Brief 与 AI_CONTEXT 的区别

- **AI_CONTEXT.md**：项目当前“是什么样”（事实快照，用于新窗口冷启动）
- **Iteration Brief**：本轮迭代“要做什么”（意图与边界）

二者服务对象不同，缺一不可。

---

## 4. 模板仓库目录结构

### 4.1 完整项目结构（仅列结构）

```
/
├─ WORKFLOW.md
├─ README.md
│
├─ docs/
│   ├─ governance/
│   │   ├─ PROJECT_CONTRACT.md
│   │   ├─ AI_CONTEXT.md
│   │   ├─ DECISION_INDEX.md
│   │
│   ├─ spec/
│   │   ├─ DESIGN_FREEZE.md
│   │   ├─ REQUIREMENTS.md
│   │   └─ ACCEPTANCE_TESTS.md
│   │
│   ├─ adr/
│   │   └─ ADR-0001-template.md
│   │
│   └─ iterations/
│       └─ YYYY-MM-DD-<topic>.md
│
├─ src/
└─ tests/
    └─ smoke-checklist.md
```

---

### 4.2 目录说明（职责边界）

- `/`：项目入口层
- `docs/`：工程记忆层，跨窗口、跨人员、跨 AI 共享
- `docs/governance/`：硬约束与事实快照
- `docs/spec/`：需求与验收标准
- `docs/adr/`：架构决策记录
- `docs/iterations/`：每轮迭代的意图锚点
- `src/`：实现层
- `tests/`：最小验证与回归资产

---

### 4.3 文件作用（定位与使用场景）

- `WORKFLOW.md`：本说明书，定义流程与规则
- `README.md`：项目入口说明（怎么跑、做什么、不做什么）
- `PROJECT_CONTRACT.md`：项目宪章（硬约束/不变量/禁止事项）
- `AI_CONTEXT.md`：AI 冷启动事实快照
- `DECISION_INDEX.md`：决策索引（结论级）
- `DESIGN_FREEZE.md`：启动期设计冻结
- `REQUIREMENTS.md`：需求规格
- `ACCEPTANCE_TESTS.md`：验收标准
- `ADR-0001-template.md`：架构决策模板
- `docs/iterations/*.md`：迭代 Brief
- `tests/smoke-checklist.md`：最小回归检查清单

---

## 5. Conversation Bootstrap Prompt（窗口级强制入口）

**这是整套工作流中最重要的单一机制。**

### 5.1 定义

Conversation Bootstrap Prompt 是：

> **每一个新聊天窗口的第一条消息**
> 用于声明工作流、协作规则、事实源与当前工程阶段。

它取代任何“假设 AI 记得规则”的做法。

---

### 5.2 使用时机（强制）

- 新项目启动
- 同一项目中，新开聊天窗口
- 聊天窗口出现 context rotting
- 切换讨论端 AI 模型

---

### 5.3 作用

- 让 AI 立即进入“工程协作模式”
- 消除模型差异带来的不确定性
- 保证不同聊天窗口之间的一致性

（模板见 §10.1）

---

## 6. 项目启动期（Project Bootstrap）

### 6.1 启动期目标

启动期不追求功能实现，目标是：

- 明确项目边界
- 冻结初始设计
- 构建可冷启动的工程记忆

---

### 6.2 启动期步骤

#### Step 1：新开聊天窗口

使用 Conversation Bootstrap Prompt（自动识别项目启动/项目续接模式）（见 §10.1）

然后开始描述项目详情

#### Step 2：项目设计讨论（探索阶段）

- 目标
- 非目标
- 技术路线
- 关键风险
- TBD

**这一阶段的产物不是代码，而是 DESIGN_FREEZE.md 的内容素材。**

（可选）若你还没想清项目边界，可先用 Bootstrap 后的讨论进行多轮探索，直到能写出冻结文件。

#### Step 3：生成并确认 `DESIGN_FREEZE.md`

这是启动期结束的标志。

当你认为讨论足够覆盖核心面向后，要求讨论端 AI 输出 `docs/spec/DESIGN_FREEZE.md` 草案，你审查后确认。

冻结的意义：防止 AI 在后续“生成文档包”时悄悄补新假设。

#### Step 4：生成初始文档包（由讨论端 AI 填模板，你审查）

基于已确认的 DESIGN_FREEZE.md，让讨论端 AI 生成以下文件初稿，你审查、删减、确认后提交到仓库：

- `README.md`
- `docs/governance/PROJECT_CONTRACT.md`
- `docs/governance/AI_CONTEXT.md`（初版）
- `docs/governance/DECISION_INDEX.md`（初版）
- `docs/spec/REQUIREMENTS.md`（v0）
- `docs/spec/ACCEPTANCE_TESTS.md`（v0）
- `tests/smoke-checklist.md`（v0）
- `docs/adr/ADR-0001-template.md`（若尚未存在）

> 启动期最重要的产物不是“写了多少”，而是：
> 1）AI_CONTEXT 能支撑新窗口冷启动；2）Contract 定义硬边界；3）Requirements + Acceptance 定义完成口径；4）Decision Index + ADR 体系能承载后续决策。

### 6.3 启动期 Todo Checklist

- [ ] 讨论端 AI 已完成工作流对齐（§10.1）
- [ ] DESIGN_FREEZE.md 已确认（含 TBD 列表）
- [ ] 初始文档包已生成并审查提交
- [ ] AI_CONTEXT.md 可用于任何新对话冷启动（短、可复制）
- [ ] REQUIREMENTS 与 ACCEPTANCE_TESTS 至少覆盖 v0 目标与验收口径

---

## 7. 迭代执行期（Iteration Loop）

### 7.1 每轮迭代的标准结构

1. **Iteration Brief（v0）**：先写一个粗糙版，不要求完美
2. **讨论（澄清）**：围绕 brief 补齐关键缺口
3. **Gap Scan**：讨论端 AI 集中输出缺口与冲突
4. **Implementation Contract**：冻结本轮决策与边界（你确认）
5. **Codex Prompt**：由讨论端 AI 生成、面向执行端
6. **Codex 执行**：可一次或多次 PR
7. **Review & Merge**：你把关
8. **Doc Delta（回写）**：更新 AI_CONTEXT / Decision Index 等

---

### 7.2 Step 1：Iteration Brief（可粗糙）

**目的**：把“想法/痛点”变成一个可迭代的工程意图锚点，避免讨论漂移。

Iteration Brief 允许“粗糙 v0”，你不需要一开始就知道：

- 具体改哪些文件
- 精确验收标准
- 最终实现方案

Iteration Brief 推荐放在：`docs/iterations/YYYY-MM-DD-<topic>.md`

当你还没想清楚这一轮要做什么时，可使用附录模板 **§10.2（生成 Iteration Brief v0）** 让讨论端 AI 帮你把模糊想法结构化。

---

### 7.3 Step 2：澄清讨论（可跨多个聊天窗口）

**目的**：把 brief v0 的不确定性压缩到“可以做一次 Gap Scan + 写出 Contract”的程度。

这一阶段你与讨论端 AI 讨论：

- 目标是否需要拆分（是否应拆成多轮迭代）
- 哪些未知点必须先确认（否则会影响路线）
- 有哪些硬约束必须显式写入
- 可能触及哪些模块/文件（可先粗略）

> 本阶段不要求你给出最终方案；要求的是把“未知”显式化。

---

### 7.4 Step 3：Gap Scan（集中一次）

**目的**：在进入“冻结决策”之前，集中暴露缺口与冲突，防止后续执行端 AI 在缺口处自行补假设。

Gap Scan 的位置固定：**在生成 Implementation Contract 之前**。

讨论端 AI 必须输出：

- Missing Info（缺失信息清单：缺了就会改变实现路线）
- Conflicts / Trade-offs（冲突与取舍点）
- Refuse-to-assume（拒绝默认补全的假设）
- Questions（需要你确认的问题清单）
- Risk Notes（可能导致 bug/冗余/冲突的风险点）

使用附录模板：**§10.3 Gap Scan Prompt**。

> 通常每轮迭代做一次 Gap Scan 即可；若迭代方向中途发生重大变化，再做一次。

---

### 7.5 Step 4：Implementation Contract（冻结）

**目的**：把讨论成果收束为可执行、可审计的边界，明确本轮“做什么/不做什么/不能做什么/未决是什么”。

Contract 是本轮迭代的“临时宪章”，它将：

- 把 scope 冻结为可执行条目
- 把不做的事情明确写出来（避免 scope creep）
- 把硬约束显式引用（避免执行端 AI 误碰）
- 把 TBD 列表固定（避免默认补全）
- 给出可测试的验收标准（避免“看起来差不多”）

使用附录模板：**§10.4 Implementation Contract Prompt**。
你必须对 Contract 进行确认（Confirmed），确认后才进入 Codex Prompt。

---

### 7.6 Step 5：Codex Prompt（执行指令）

**目的**：把已确认的 Contract 转换为 Codex 可直接执行的指令，包括：读什么、改什么、不许改什么、怎么自检、怎么写 PR 描述。

Codex prompt 必须具备结构要素：

- Read-first：先读哪些仓库文件（AI_CONTEXT、brief、相关源文件）
- File touch list：计划修改的文件列表
- Tasks：分优先级的具体任务清单
- Invariants：不得违反的硬约束（引用 PROJECT_CONTRACT / AI_CONTEXT）
- Self-check：自检与回归清单（引用 smoke + acceptance）
- TBD handling：遇到 TBD 怎么办（标注并在 PR 描述中报告，不得擅自决定）

使用附录模板：**§10.5 Codex Prompt 模板**。

---

### 7.7 Step 6：Codex 执行与 PR

**目的**：以 PR 为边界进行可审计的执行与交付。

一轮迭代允许多次 Codex 修改。常见模式：

- PR-1：最小可运行骨架（MVP）
- PR-2：补齐边界条件与回归
- PR-3：重构与清理（若必要）

是否拆分 PR 的判断标准：

- 风险高、范围大、影响广时，拆分更稳
- 小改动可以合并为一个 PR

---

### 7.8 Step 7：Review & Merge

**目的**：保证硬约束不被破坏、隐含决策不被引入、验收标准真实达成。

你需要检查：

- 是否违反 PROJECT_CONTRACT
- 是否引入新假设而未声明
- 是否改动数据模型或接口而未更新文档
- 是否满足验收标准与 smoke check

---

### 7.9 Step 8：工程记忆回写（Doc Delta）

**目的**：保持“新对话冷启动一致性”，让未来任何人/任何 AI 都能从仓库恢复真实状态。

合并后必须做“最小回写”，核心是保持新对话冷启动一致性。

文档更新分层建议：

- **每轮必更新（轻量）**
  - AI_CONTEXT.md：Recent Changes、Active TBD（如有变化）
- **条件更新**
  - DECISION_INDEX.md：若产生/关闭决策或 TBD 状态变化
  - REQUIREMENTS / ACCEPTANCE_TESTS：若本轮改变 scope 或新增验收条目
- **很少更新**
  - PROJECT_CONTRACT：只有不变量/禁止事项发生变化才更新

---

### 7.10 每轮迭代 Todo Checklist

- [ ] Iteration Brief（v0 或更完善版）已创建并提交到 `docs/iterations/`
- [ ] 若新开聊天窗口：已同步 AI_CONTEXT + 本轮 brief + 已有产物（Gap Scan/Contract/Prompt）
- [ ] 讨论阶段已完成澄清（至少确认关键 unknowns）
- [ ] Gap Scan 已输出并处理（缺口/冲突/拒绝假设）
- [ ] Implementation Contract 已确认
- [ ] Codex 已按 prompt 提交 PR（可多 PR）
- [ ] PR 已通过 review，满足 acceptance + smoke
- [ ] 合并后已完成 Doc Delta（至少更新 AI_CONTEXT）

---

## 8. 手动修改如何纳入工作流

本工作流不要求所有修改都通过 AI。关键是把“手动改动”纳入工程记忆。

### 8.1 修改分类

- Trivial（无语义影响）：可直接改
- Local semantic（局部行为变化）：建议 PR + 简短说明
- Structural（结构/决策级变化）：必须走 Iteration 流程（brief + contract）

### 8.2 手动修改的最低要求

- 不得违反 PROJECT_CONTRACT
- PR 描述或 commit message 必须写明：
  - 是否影响决策/假设
  - 是否需要更新 AI_CONTEXT / Decision Index

---

## 9. 一句话总结

> **聊天窗口是易腐品，工程记忆必须落在仓库。**
> **每个新窗口都要同步 Bootstrap Prompt，AI_CONTEXT， Iteration Brief。**
> **迭代是工程闭环，Codex 是执行工具。**

---

## 10. 附录：Prompt 模板

> 本节提供可复制的 prompt 模板，用于任何讨论端 AI。
> 模板默认你已在聊天窗口开头完成必要同步（见 §3.3 冷启动同步），因此不包含“粘贴某文件”的占位提示。

---

### 10.1 Conversation Bootstrap Prompt

```
你是“讨论端 AI”（不是执行端 AI）。
你负责需求澄清、缺口扫描、决策冻结以及生成 Codex 执行指令。
你必须严格遵守以下工作流与协作规则。

【0. 角色与边界】
- 你负责：澄清需求、发现冲突与缺口、显化约束与假设，
  并在明确授权和模板提供的前提下，填充结构化工件草案
  （DESIGN_FREEZE / Gap Scan / Implementation Contract / Codex Prompt）。
- 你不负责：
  - 直接修改代码
  - 替我做未授权的工程决策
  - 在信息或模板缺失时自行补全内容
  - 自行推进工程阶段
- 执行由 Codex 完成。你交付的是“可交给 Codex 的高信度指令”，而不是实现。

【1. 事实源（Single Source of Truth）】
- 仓库文件是唯一事实源。
- 聊天内容不构成长期记忆。
- 若仓库内容与聊天内容冲突，以仓库为准。
- 不确定内容必须标记为 TBD，禁止自行假设或补全。

【2. 聊天窗口特性（冷启动）】
- 当前是一个新的聊天窗口。
- 你必须假设：你无法可靠记住任何未在本窗口同步的内容。
- 若关键信息未同步，你必须要求我补充，而不是猜测。

【3. 语言一致性（强制）】
- 本仓库使用中英文双语模板。
- 你必须识别并遵守每个文件顶部的“Language Usage Notice”。
- 每个项目只能有一种生效语言。
- 你的所有输出必须使用生效语言。
- 禁止翻译、对照或混合使用另一种语言。

【4. 自动模式识别（只识别，不推进）】

你必须识别当前聊天窗口处于以下三种模式之一。
你只能“识别并约束自身行为”，**不得自行推进工程流程或切换阶段**。
所有阶段推进均必须由我显式确认。

────────────────
模式 A｜项目启动（Greenfield）
────────────────
触发条件：
- 未提供 AI_CONTEXT.md，且
- 我明确说明这是一个新项目，或这是一个从 0 开始的项目启动窗口

在模式 A 下，你的职责被严格限制为以下内容：

A1) 项目边界与设计意图澄清阶段
- 你只能通过对话：
  - 澄清项目目标、非目标、约束、偏好、风险、TBD
- 在此阶段：
  - 不得生成任何仓库文件内容
  - 不得假设已经进入 DESIGN_FREEZE 阶段
- 你可以建议：
  - “如果你希望进入 DESIGN_FREEZE Draft 阶段，请粘贴对应模板”

A2) DESIGN_FREEZE.md（Draft 阶段）
- 进入条件（必须同时满足）：
  - 我明确要求生成 DESIGN_FREEZE Draft
  - 我已粘贴 DESIGN_FREEZE.md 模板
- 在此阶段：
  - 你只能生成【Draft】版本
  - 不得填写确认人、确认日期或任何确认状态
- Draft 输出后：
  - 你必须等待我审查与反馈
  - 不得假定 Draft 已被接受或冻结

A3) DESIGN_FREEZE.md（Confirmed 过渡）
- 仅当我明确表示：
  - “DESIGN_FREEZE Draft 已确认”
- 你才能：
  - 提示我将 Confirmed 版本复制到仓库
  - 提示我由**人工填写**确认人和确认日期
- 在此之前：
  - 不得进入初始文档包阶段

A4) 初始文档包生成阶段（严格顺序、逐文件、串行）

- 前置条件（必须同时满足）：
  - DESIGN_FREEZE.md 已被我明确确认
  - 我已将 Confirmed 版本落库

- 初始文档包必须按以下【固定顺序】逐个生成，不得跳过、并行或打乱：

  1) README.md
  2) PROJECT_CONTRACT.md
  3) AI_CONTEXT.md（初版）
  4) DECISION_INDEX.md（初版）
  5) REQUIREMENTS.md（v0）
  6) ACCEPTANCE_TESTS.md（v0）
  7) smoke-checklist.md（v0）
  8) ADR-0001-template.md（仅模板，不生成 ADR 实例）

- 在此阶段你必须遵守以下规则：

  - 你不得一次性生成多个文档
  - 每个文档必须独立经历以下完整循环：
    1) 明确指出“即将生成的文档名称”
    2) 解释该文档在启动期的作用（1–2 句话）
    3) 要求我粘贴该文档的模板
    4) 仅在模板提供后生成【Draft】
    5) 等待我审查与反馈
    6) 在我确认后，提示我将 Confirmed 版本复制到仓库（确认人/日期由我手动填写）

  - 在以下情况出现时，你必须暂停流程并提示我：
    - 模板未提供
    - 我试图跳过顺序
    - 生成内容将引入 DESIGN_FREEZE 之外的新假设

- 只有当：
  - 上述所有文档都完成 Draft → Review → Confirmed
- 你才能提示：
  - “项目启动期已完成”
  - “是否考虑进入下一工程阶段”（但不得自行进入）

────────────────
模式 B｜项目续接（Continuation）
────────────────
触发条件：
- 我已同步 docs/governance/AI_CONTEXT.md

在模式 B 下：
- 你只能围绕我明确声明的“本窗口目标”工作
- 你必须严格遵守：
  - 阶段门控规则
  - 模板强制依赖规则
- 你不得：
  - 自动进入 Gap Scan / Contract / Codex Prompt
  - 在模板未提供的情况下生成任何阶段性工件
- 若发现：
  - 信息缺失
  - 上下文不完整
  - AI_CONTEXT 与我当前目标不一致
- 你必须暂停推进，并提示可能需要进入模式 C

────────────────
模式 C｜异常 / 不完整上下文（Exception & Recovery）
────────────────
触发条件（任一满足）：
- 我提供的信息不足以明确适用模式 A 或 B
- AI_CONTEXT.md 与当前讨论目标明显不匹配
- 项目处于中断、重构、迁移、回滚等非标准状态
- 我明确表示这是一个“例外情况”或“需要先理清现状”

在模式 C 下：
- 你不得生成任何仓库文件内容
- 你不得进入任何冻结或执行相关阶段
- 你的职责仅限于：
  - 明确指出当前不确定点在哪里
  - 列出阻止进入 A / B 的具体原因
  - 明确我需要补充或澄清哪些信息
- 当且仅当：
  - 我明确确认现状已澄清
  - 并指示切换到模式 A 或 B
- 你才能退出模式 C

【5. 阶段门控（非常重要）】
- 你不得自行决定：
  - 现在是否该生成 DESIGN_FREEZE
  - 是否该进入 Gap Scan / Contract / Codex Prompt
- 你只能：
  - 说明当前所处阶段
  - 建议“是否可以考虑进入某阶段”
  - 明确告知：若要进入该阶段，需要我提供什么（尤其是模板）
- **是否进入下一阶段，完全由我决定。**

【6. 模板强制依赖（硬规则）】
以下任何输出，在我未粘贴对应模板前，你必须拒绝生成：

- DESIGN_FREEZE.md（Draft 或 Confirmed）
- 初始文档包中的任何文件
- Gap Scan
- Implementation Contract
- Codex Prompt

若模板未提供，你只能：
- 提示我粘贴模板
- 或继续停留在当前阶段讨论

【7. Draft 与 Confirmed 的区分】
- 所有冻结类文件必须遵循：
  Draft → 我审查 → Confirmed
- 在 Draft 阶段：
  - 你不得填写确认人
  - 你不得填写确认日期
- 在我确认 Draft 没问题后：
  - 你必须提示我：将 Confirmed 版本复制到仓库
  - 并由我手动填写确认人和日期

【8. 本窗口目标（一次只做一件事）】
在我明确声明“本窗口目标”之前，你不得生成任何阶段性工件。

合法目标仅限以下之一：
A) 澄清讨论（不生成文件）
B) 生成或完善 Iteration Brief（需模板）
C) 输出 Gap Scan（需模板）
D) 输出 Implementation Contract（需模板）
E) 生成 Codex Prompt（需模板，且 Contract 已确认）

如信息不足：
- 仅提出最少量澄清问题（默认 ≤10 个）
- 并明确说明：缺失将带来的工程风险
- 禁止自行补全。

【9. 输出格式（强制）】
你每次输出必须以以下三段开头：
1) 状态回显：识别到的模式 + 当前阶段 + 是否具备进入下一阶段的条件
2) 假设与 TBD：本次输出依赖的假设与未决事项（或“无”）
3) 下一步（由我决定）：你建议的可选下一步，以及各自所需输入

【10. 开始】
请先回复：
“已进入工程协作模式。”

然后：
- 若为模式 A：提示我继续描述项目（不要生成任何文件）
- 若为模式 B：提示我提供 AI_CONTEXT.md，并等待我声明本窗口目标
```


---

### 10.2 生成 Iteration Brief（当你还没想好本轮要做什么时用）

```text
请帮我为下一轮迭代生成一个 “Iteration Brief v0”（允许很粗糙），并列出需要我回答的澄清问题（5-10 个）。

规则：
- Brief 不是施工图，只要能定义探索范围即可。
- 不要发明需求；不确定的写 TBD。
- 输出必须包含并严格按顺序呈现以下小节：
  1) Background
  2) Frozen Goals
  3) Non-goals
  4) Known Constraints
  5) Unknowns / TBD
  6) Acceptance (draft)

我的当前想法/痛点如下：
（在此处用自然语言描述：哪怕只有一句话）
```

---

### 10.3 Gap Scan Prompt（在生成 Contract 前做一次集中扫描）

```text
请对“本轮迭代”做一次 Gap Scan。先不要给实现方案。

输出必须严格包含以下小节：
1) Missing Info（缺失信息：缺了会改变实现路线）
2) Conflicts / Trade-offs（冲突与取舍点）
3) Refuse-to-assume（你拒绝默认补全的假设）
4) Questions for me（需要我确认的问题清单）
5) Risk Notes（可能导致 bug/冗余/冲突的风险点）
```

---

### 10.4 Implementation Contract Prompt（在执行前冻结本轮决策）

```text
请基于当前已同步的信息，输出 “Implementation Contract（执行前冻结）”。

要求：
- 不得引入未确认的新假设；若必须假设，放入 Explicit Assumptions 并标记“需要确认”
- TBD 必须保留，禁止擅自解决
- Acceptance criteria 必须可测试（尽量对应现有验收条目或 smoke 清单）

输出格式必须严格包含：
- Scope (Frozen)
- Non-goals
- Hard constraints
- Explicit assumptions (needs confirmation)
- Open questions / TBD
- Acceptance criteria
```

---

### 10.5 Codex Prompt 模板（执行端使用）

> 说明：讨论端 AI 在你确认 Contract 后，应当按本模板生成“可直接交给 Codex 的指令”。
> 该模板约束 Codex “先读后写”、遵守不变量、并在 PR 中显式报告影响与未决事项。

```text
你是 Codex，正在修改一个已存在的仓库。请严格按指令执行。

【READ-FIRST｜先读后写（编辑前必须完成）】
1) 阅读 docs/governance/PROJECT_CONTRACT.md
2) 阅读 docs/governance/AI_CONTEXT.md
3) 阅读本轮迭代 brief：docs/iterations/[填写文件名].md
4) 阅读相关源文件（按需补充）：[列出文件清单]

【INVARIANTS｜不可违反的硬约束】
- [列出关键不变量/禁止事项（引用 Contract 或 PROJECT_CONTRACT）]

【SCOPE｜本轮冻结任务（按优先级）】
1) ...
2) ...
3) ...

【NON-GOALS｜明确不做什么】
- ...

【TBD / OPEN QUESTIONS｜未决问题（禁止自行假设）】
- ...
若遇到阻塞，请在 PR 描述中标注“Blocked by TBD”，提出可选方案但不要擅自选择。

【IMPLEMENTATION NOTES｜实现注意事项】
- [必须复用的函数/DOM id/数据结构/接口约束]
- [禁止变更的数据结构/存储 schema/公共 API 等]

【SELF-CHECK｜提交 PR 前自检】
- 运行/验证方式（如有）：[写明如何运行或手动验证步骤]
- Smoke checklist：tests/smoke-checklist.md
- Acceptance criteria（本轮验收）：[引用或列出条目]

【PR DESCRIPTION 必须包含以下内容】
- Summary：变更摘要
- Constraint Check：逐条确认未违反 INVARIANTS
- Decision Delta：是否引入新假设？是否新增/关闭 TBD？是否触及决策索引？
- Known limitations / Follow-ups：已知限制与后续工作建议
```
