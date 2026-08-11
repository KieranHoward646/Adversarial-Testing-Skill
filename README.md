# Adversarial Testing Skill

> Multi-AI collaborative adversarial testing workflow for WorkBuddy.  
> 面向 WorkBuddy 的多 AI 协作对抗测试工作流技能。

[English](#english) | [中文](#中文)

---

## English

### What is this?

A WorkBuddy skill that orchestrates a two-AI adversarial testing pipeline:

1. **Developer AI** — Analyzes the target product and produces a structured summary covering every entry point, input constraint, error scenario, and test target across six categories (boundary, injection, auth, concurrency, exception, business logic).
2. **Adversarial AI** — Reads only the summary (zero knowledge of source code), safely executes destructive tests, auto-fixes issues within safe boundaries, and produces a comprehensive test report.

The adversarial AI is intentionally **blind** to the product internals — it only sees the structured summary. This mirrors real-world attack scenarios where attackers must infer behavior from external interfaces.

### Installation

Place this directory under your WorkBuddy skills path:

```
~/.workbuddy/skills/adversarial-testing/
```

Or copy it to a project-level path:

```
<project>/.workbuddy/skills/adversarial-testing/
```

### Usage

| What you say | What happens |
|-------------|-------------|
| `@adversarial-testing <product>` | **Full pipeline** (default) — two independent AI agents collaborate. Developer analyzes & summarizes, then Adversarial tests & fixes. |
| `@adversarial-testing sequential <product>` | Same two phases, but with a **review gate** between them. You can inspect the summary before testing begins. |
| `@adversarial-testing developer <product>` | **Summary only** — just produce the adversarial test summary, no testing. |
| `@adversarial-testing adversarial <summary-path>` | **Test only** — run tests against an existing summary file. |

Chinese triggers also work: "对抗测试", "安全测试", "只要总结", "只要测试".

### Output Files

| File | Produced by |
|------|-------------|
| `adversarial-test-summary-<product>.md` | Developer AI (Phase 1) |
| `adversarial-test-report-<product>.md` | Adversarial AI (Phase 2) |

### Safety Guarantees

The Adversarial AI enforces a non-negotiable safety protocol:

- ❌ No `DROP`, `TRUNCATE`, or unconditional `DELETE`
- ❌ No modification of production data or config files
- ❌ No real external requests (emails, SMS, payments)
- ❌ Injection testing stops immediately if a real vulnerability is found
- ✅ All destructive operations require explicit safe-boundary declarations in the summary

### Files

```
adversarial-testing/
├── SKILL.md                           # Orchestration layer (workflow, decision tree, agent spawning)
├── README.md                          # This file
├── LICENSE                            # MIT
└── references/
    ├── for-developer-ai.md            # Developer AI output specification (9-section format)
    └── for-adversarial-ai.md          # Adversarial AI safety & testing specification (4-phase workflow)
```

---

## 中文

### 这是什么？

一个 WorkBuddy 技能，编排双 AI 对抗测试流水线：

1. **开发者 AI** — 分析目标产品，输出结构化摘要，覆盖所有入口、输入约束、错误场景，以及六大类测试目标（边界 / 注入 / 权限 / 并发 / 异常 / 业务逻辑）。
2. **对抗测试 AI** — 仅阅读摘要（对源码零知识），安全执行破坏性测试，在安全边界内自动修复问题，输出完整测试报告。

对抗测试 AI 故意被设计为对产品内部实现**盲测** —— 它只能看到结构化摘要。这模拟了真实的攻击场景：攻击者只能从外部接口推断系统行为。

### 安装

将本目录放入 WorkBuddy 技能路径：

```
~/.workbuddy/skills/adversarial-testing/
```

或放入项目级路径：

```
<project>/.workbuddy/skills/adversarial-testing/
```

### 使用方式

| 对 WorkBuddy 说 | 效果 |
|-----------------|------|
| `@adversarial-testing <产品>` | **全流水线**（默认）— 两个独立 AI Agent 协作。开发者分析出总结，对抗测试者执行测试并修复。 |
| `@adversarial-testing sequential <产品>` | 同上，但在两阶段之间增加**审核关卡**，你可以先审查摘要再放行测试。 |
| `@adversarial-testing developer <产品>` | **仅出总结** — 只产��对抗测试摘要，不执行测试。 |
| `@adversarial-testing adversarial <摘要路径>` | **仅执行测试** — 对已有摘要文件执行对抗测试。 |

中文触发词同样有效："对抗测试"、"安全测试"、"只要总结"、"只要测试"。

### 输出文件

| 文件 | 产出者 |
|------|--------|
| `adversarial-test-summary-<产品>.md` | 开发者 AI（阶段1） |
| `adversarial-test-report-<产品>.md` | 对抗测试 AI（阶段2） |

### 安全保障

对抗测试 AI 强制执行不可绕过的安全协议：

- ❌ 不执行 `DROP`、`TRUNCATE`、无条件 `DELETE`
- ❌ 不修改生产数据或配置文件
- ❌ 不发送真实外部请求（邮件、短信、支付）
- ❌ 注入测试发现真实漏洞后立即停止同类测试
- ✅ 所有破坏性操作需在摘要 §5 里明确声明安全边界

### 文件结构

```
adversarial-testing/
├── SKILL.md                           # 编排层（工作流、决策树、Agent 派生逻辑）
├── README.md                          # 本文件
├── LICENSE                            # MIT
└── references/
    ├── for-developer-ai.md            # 开发者 AI 输出规范（9 章节格式）
    └── for-adversarial-ai.md          # 对抗测试 AI 安全与测试规范（4 阶段流程）
```

---

## License

MIT © 2026 Kieran Howard
