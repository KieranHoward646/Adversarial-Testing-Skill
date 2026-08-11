---
name: adversarial-testing
description: |
  Multi-AI adversarial testing workflow. Two AI agents collaborate: a Developer AI produces a structured product summary with test targets, then an Adversarial AI safely executes tests and auto-fixes issues. This skill should be used when the user wants to run adversarial testing on any software product, API, or feature — especially when they say "对抗测试", "adversarial testing", "安全测试", or want an AI to try to break a product and fix what it finds. Supports three sub-modes: developer (summary only), adversarial (test only), and full (end-to-end pipeline). Default full pipeline uses true multi-AI collaboration with separate agent contexts — the adversarial AI has ZERO knowledge of product internals beyond the summary.
agent_created: true
---

# Adversarial Testing — Multi-AI Collaboration Workflow

## Overview

Orchestrate a two-phase adversarial testing pipeline. **By default, uses true multi-AI collaboration**: two independent Agent instances with separate context windows — the Developer AI deeply understands the product, while the Adversarial AI sees ONLY the structured summary (true blind adversarial stance).

## When to Use

Trigger when the user says any of:
- "对抗测试" / "adversarial testing"
- "安全测试" / "penetration test this"
- "测试这个产品的安全性" / "find vulnerabilities in"
- "让AI攻击测试一下" / "let an AI try to break this"
- "run adversarial testing on [product/feature/API]"
- "@adversarial-testing"

## Workflow Decision Tree

```
User: "对抗测试 X"

├── User says "只要总结" / "developer only"?
│   └── YES → Developer-Only Mode (single agent, in-session)
│
├── User says "只要测试" + provides summary file?
│   └── YES → Adversarial-Only Mode (single agent, in-session)
│
├── User says "sequential" / "逐步" / "先看总结再测试"?
│   └── YES → Sequential Mode (same AI, two phases with user review gate)
│
└── Default → Multi-AI Mode (two independent agents, true collaboration)
```

## Full Pipeline: Multi-AI Mode (Default)

This is the recommended mode. Two independent Agent instances with **separate context windows** — the adversarial AI has no knowledge of the product's internal code, only the structured summary.

### Architecture

```
┌─────────────────────────────────────────────────────┐
│                  Orchestrator (You)                  │
│                                                     │
│  Step 1: Spawn Developer Agent                      │
│  ┌───────────────────────────────────────┐          │
│  │  Agent: developer-ai                  │          │
│  │  Reads: references/for-developer-ai.md│          │
│  │  Does: Analyze product → Output summary│          │
│  │  Output: adversarial-test-summary.md   │          │
│  └───────────────────────────────────────┘          │
│                    │                                 │
│                    ▼ (summary file is the ONLY       │
│                       information passed)            │
│  Step 2: Spawn Adversarial Agent                    │
│  ┌───────────────────────────────────────┐          │
│  │  Agent: adversarial-ai                │          │
│  │  Reads: summary.md +                  │          │
│  │         references/for-adversarial-ai.md│         │
│  │  Does: Safety check → Test → Fix       │          │
│  │  Output: adversarial-test-report.md    │          │
│  └───────────────────────────────────────┘          │
│                                                     │
│  Step 3: Report to user                             │
└─────────────────────────────────────────────────────┘
```

**Key advantage**: The adversarial AI is **blind** — it sees only the structured summary, not the source code. This mirrors real-world attack scenarios where attackers don't have access to internals.

### Step 1: Spawn Developer Agent

Use the Agent tool to spawn an independent Developer AI agent:

```
Agent tool parameters:
  name: "developer-ai"
  subagent_type: "general-purpose"
  description: "Produce adversarial testing summary for [product]"
  prompt: |
    You are the Developer AI for adversarial testing. Your job is to produce a structured
    adversarial testing summary.

    **Target product**: [describe what to test — file paths, repo location, etc.]

    **Instructions**:
    1. Read the reference spec at:
       C:/Users/DELL/.workbuddy/skills/adversarial-testing/references/for-developer-ai.md
    2. Analyze the target product thoroughly: read source code, understand architecture,
       identify ALL entry points (API endpoints, function signatures, CLI, UI inputs).
    3. Produce the summary STRICTLY following the format in the reference spec.
    4. Write output to: <workspace>/adversarial-test-summary-<product-name>.md
    5. Return: the file path of the saved summary and a brief confirmation.
```

Run this agent in the **foreground** (need the summary before proceeding). Wait for it to complete and confirm the summary file exists.

### Step 2: Spawn Adversarial Agent

After the summary file is confirmed:

```
Agent tool parameters:
  name: "adversarial-ai"
  subagent_type: "general-purpose"
  description: "Execute adversarial tests against [product]"
  prompt: |
    You are the Adversarial AI for adversarial testing. Your job is to safely execute
    destructive tests and fix discovered issues.

    **Summary file**: <workspace>/adversarial-test-summary-<product-name>.md

    **Instructions**:
    1. Read the summary file at the path above.
    2. Read the safety & testing spec at:
       C:/Users/DELL/.workbuddy/skills/adversarial-testing/references/for-adversarial-ai.md
    3. Follow the 4-phase workflow from the spec:
       Phase 1: Parse summary, verify completeness
       Phase 2: Safety verification (MANDATORY — check all forbidden operations)
       Phase 3: Execute tests in strict order (boundary → injection → auth → concurrency → exception → business logic)
       Phase 4: For each FAIL, determine if safe to auto-fix. Fix or escalate.
    4. Write final report to: <workspace>/adversarial-test-report-<product-name>.md
    5. Return: summary of findings (PASS/FAIL counts, any critical issues).

    **IMPORTANT**: You have NO knowledge of the product internals. You ONLY have the summary.
    This is by design — you are a true adversarial tester. Use ONLY the summary to understand
    the product. If the summary is incomplete, note it in the report.
```

Run this agent in the **foreground** (need the report).

### Step 3: Report to User

After both agents complete:
- Present the report path to the user
- Summarize key findings: total tests, PASS/FAIL/SKIP counts, any critical vulnerabilities
- List what was auto-fixed vs. what needs human attention

## Sequential Mode (with Review Gate)

When user says "sequential" / "逐步". Use when they want to review the summary before unleashing the adversarial AI.

```
Same session, same AI plays both roles:

Phase 1: Read references/for-developer-ai.md → Analyze → Produce summary → Present to user
WAIT for user to say "go" or "继续" or "开始测试"

Phase 2: Read references/for-adversarial-ai.md + summary → Execute → Report
```

The handoff gate gives the user a chance to:
- Verify the summary covers everything
- Adjust test targets before execution
- Cancel or scope down if needed

## Developer-Only Mode

When user says "只要总结" / "developer only".

```
Read references/for-developer-ai.md
→ Analyze product → Produce summary → Save as .md → Done
```

Output: `<workspace>/adversarial-test-summary-<product>.md`

## Adversarial-Only Mode

When user says "只要测试" / "adversarial only" + provides summary file path.

```
Read references/for-adversarial-ai.md
→ Read summary → Safety check → Execute tests → Fix → Report → Done
```

If no summary path provided, ask: "请提供开发者AI生成的摘要文件路径。"

## Output Files

| Phase | Output | Convention |
|-------|--------|------------|
| Phase 1 (Developer) | Adversarial test summary | `<workspace>/adversarial-test-summary-<product>.md` |
| Phase 2 (Adversarial) | Test report | `<workspace>/adversarial-test-report-<product>.md` |

## Resources

### references/for-developer-ai.md
Complete specification for the Developer AI output format. Load before Phase 1. Covers all nine required sections, writing standards, risk level definitions, and a worked example.

### references/for-adversarial-ai.md
Complete specification for Adversarial AI workflow. Load before Phase 2. Covers safety verification protocol, six-category test execution order, fix rules, and final report format.
