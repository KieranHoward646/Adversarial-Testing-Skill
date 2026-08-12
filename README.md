# ⚔️ Adversarial Testing Framework

<p align="center">
  <b>让 AI 攻击你的产品——在真正的攻击者下手之前。</b>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/version-2.3-blue" alt="version">
  <img src="https://img.shields.io/badge/license-MIT-green" alt="license">
  <img src="https://img.shields.io/badge/platform-WorkBuddy%20%7C%20Cursor%20%7C%20Claude%20%7C%20Any%20AI-purple" alt="platforms">
  <img src="https://img.shields.io/badge/payloads-8%20libraries-orange" alt="payloads">
  <img src="https://img.shields.io/badge/adapters-4%20platforms-blue" alt="adapters">
  <img src="https://img.shields.io/badge/theory-5%20guides-teal" alt="theory">
</p>

---

<p align="center">
  <a href="#-what-is-this">What</a> •
  <a href="#-quick-start">Quick Start</a> •
  <a href="#-architecture">Architecture</a> •
  <a href="#-features">Features</a> •
  <a href="#-choose-your-platform">Platforms</a> •
  <a href="#-framework-stats">Stats</a>
</p>

> **English** | [中文](#中文)

---

## ⚡ What Is This?

A **battle-tested, multi-platform adversarial testing framework** — not just a skill, not just a guide. A complete **theory + tools + payloads + CI/CD** ecosystem that turns any AI coding assistant into a security red team.

### The Core Idea

```
You → "对抗测试 my-login-API"
         │
         ▼
  ┌──────────────┐    ┌──────────────────┐
  │ Developer AI │───▶│ Structured        │   Only the summary
  │ (reads code) │    │ Summary           │──▶is passed through
  └──────────────┘    └──────────────────┘
                              │
                              ▼
                       ┌──────────────┐
                       │ Adversarial  │   Has ZERO knowledge
                       │ AI (blind)   │   of source code
                       └──────┬───────┘
                              │
              ┌───────────────┼───────────────┐
              ▼               ▼               ▼
         🛡️ Test        🔧 Auto-Fix      📊 Report
         (8 attack      (diff patches)   (SARIF+JSON
          categories)                     +HTML+MD)
```

**The adversarial AI is intentionally blind** — just like a real attacker who only sees the API surface.

### Why This Exists

AI systems have a new kind of attack surface: **natural language**. Prompt injection cost the world ~$2.3B in 2025. Traditional testing tools can't test what they can't parse. This framework bridges that gap — AI-powered AI testing.

---

## 🚀 Quick Start

### WorkBuddy (30 seconds)

```bash
git clone git@gitee.com:kieranhoward/adversarial-testing-skill.git \
  ~/.workbuddy/skills/adversarial-testing
```

Then just say:

```
@adversarial-testing my-product
```

Two independent AI agents spawn — Developer analyzes, Adversarial attacks.

### Any Other Platform

```bash
git clone git@gitee.com:kieranhoward/adversarial-testing-skill.git
cd adversarial-testing-skill

# Cursor
cp adapters/cursor/.cursorrules /your-project/
cp tools/developer-ai-spec.md /your-project/.cursor/rules/dev-spec.md
cp tools/adversarial-ai-spec.md /your-project/.cursor/rules/adv-spec.md

# Claude Code
cp adapters/claude-code/CLAUDE.md /your-project/

# Any AI (TRAE, Codex, Copilot, Windsurf...)
# Use self-contained prompts in adapters/generic/
```

---

## 🏗 Architecture

```
adversarial-testing/
│
├── 📚 theory/               "The Why & How"
│   ├── 01-overview           Core concepts
│   ├── 02-attack-taxonomy    4-class attack system (A/B/C/D)
│   ├── 03-methodology        4-phase workflow
│   ├── 04-case-studies       2023-2026 incident timeline
│   └── 05-roadmap            30/60/90-day plan
│
├── 🔧 tools/                "The Execution Engine"
│   ├── developer-ai-spec     AI instruction: produce summary
│   ├── adversarial-ai-spec   AI instruction: test + fix
│   │
│   ├── templates/            10 battle-tested templates
│   │   ├── summary            Report structures
│   │   ├── report
│   │   ├── rules-of-engagement  Legal/safety boundary
│   │   ├── severity-scoring     CVSS-like 0-10 system
│   │   ├── audit-log-schema     Replayable JSON schema
│   │   ├── report-formats       SARIF / JSON / HTML
│   │   ├── operation-approval   7-rule hard safety engine
│   │   ├── patch-generation     Auto-fix diff format
│   │   ├── docker-sandbox       Container isolation
│   │   └── trend-report         Historical comparison
│   │
│   └── payloads/             8 attack libraries
│       ├── injection           SQL/XSS/SSTI/Command
│       ├── jwt                 Algorithm confusion, claims
│       ├── ssrf                Cloud metadata, bypasses
│       ├── deserialization     Pickle/Java/PHP/Node/.NET
│       ├── file-upload         Shells, Zip Slip, magic bytes
│       ├── llm                 Jailbreak, prompt injection
│       ├── ssl                 Certificate mutation
│       └── tech-stack-mapping  Smart payload selection
│
├── 🔌 adapters/             "Multi-Platform"
│   ├── cursor/               .cursorrules + spec files
│   ├── claude-code/           CLAUDE.md
│   └── generic/               Self-contained prompts
│
├── ⚙️ .github/workflows/    "CI/CD"
│   └── adversarial-test.yml  PR-triggered testing
│
├── 👥 community/             "Ecosystem"
│   └── test-case-template    Submit your own payloads
│
└── 🤝 CONTRIBUTING.md        How to contribute
```

---

## ✨ Features

### 🎯 Attack Coverage

| Class | Scope | Payloads |
|-------|-------|:------:|
| **A — General** | 6 categories (boundary, injection, auth, concurrency, exception, logic) | 100+ |
| **B — LLM/AI** | 8 categories (prompt injection, jailbreak, system leak, poison, agent, multimodal...) | 50+ |
| **C — Network** | SSL/TLS certificate validation, protocol state machines | 30+ |
| **D — ML** | Evasion, poisoning, model extraction | 20+ |

### 🛡️ Safety by Design

Three-layer defense, not prompt-based wishful thinking:

```
Layer 1: Docker Sandbox    ← OS-level isolation (read-only FS, cap-drop)
Layer 2: Approval Chain    ← 7 hard-coded rules (DROP/SYSTEM/PROD = instant BLOCK)
Layer 3: AI Constraints    ← Natural language guardrails (last resort)
```

### 📊 Enterprise-Grade Reporting

- **SARIF** → GitHub Security Tab (one-line CI integration)
- **JSON** → Machine-consumable (SOAR/SIEM/Dashboard)
- **Markdown** → Human review
- **HTML** → Visual dashboard
- **Severity Scoring** → CVSS-like 0-10 (exploitability × impact × sensitivity × fixability)
- **Audit Log** → Every step replayable from JSON
- **Coverage Tracking** → Which endpoints tested, which missed, and why
- **Trend Reports** → Security posture over time

### 🔄 CI/CD Native

```yaml
# .github/workflows/adversarial-test.yml
# PR opened → auto-run adversarial tests → post results as PR comment
# Critical findings? Upload to GitHub Security Tab as SARIF
```

### 🌍 Multi-Platform

| Platform | Multi-Agent? | Setup |
|----------|:-----------:|-------|
| **WorkBuddy** | ✅ Native | `@adversarial-testing` |
| **Cursor** | ✅ 2 sessions | Copy rules |
| **Claude Code** | ✅ 2 terminals | Copy CLAUDE.md |
| **Any AI** | ⚠️ Manual | Copy prompts |

---

## 📊 Framework Stats

| Metric | Count |
|--------|:----:|
| Total files | 38 |
| Theory guides | 5 |
| Executable templates | 10 |
| Attack payload libraries | 8 |
| Platform adapters | 4 |
| Payload categories covered | 18 |
| Safety rules (hard-coded) | 7 |
| Real-world case studies | 15+ |

---

## 🗺️ Where to Start

| You are a... | Start here |
|-------------|-----------|
| **New user** | `theory/01-overview.md` → understand the "why" |
| **Developer testing your own product** | `@adversarial-testing my-product` |
| **Security researcher** | `theory/02-attack-taxonomy.md` + `tools/payloads/` |
| **Team lead** | `theory/05-roadmap.md` → 30/60/90 plan |
| **Contributor** | `CONTRIBUTING.md` + `community/test-case-template.md` |

---

## 📜 License

MIT © 2026 Kieran Howard

---

<p align="center">
  <sub>Built with • anger at insecure software • respect for real red teams • and a lot of Markdown</sub>
</p>

---

# 中文

## ⚡ 这是什么？

一个**经过实战验证的、跨平台的对抗测试框架**——不只是 skill，不只是指南。一套完整的**理论 + 工具 + 载荷库 + CI/CD** 生态系统，把任何 AI 编程助手变成安全红队。

### 核心理念

两个 AI 协作，但彼此**信息隔离**——开发者 AI 深度理解代码，对抗测试 AI **完全看不到源码**，只能从结构化摘要推断系统行为。这和真实攻击者的处境一模一样。

## 🚀 快速开始

### WorkBuddy（30 秒）

```bash
git clone git@gitee.com:kieranhoward/adversarial-testing-skill.git \
  ~/.workbuddy/skills/adversarial-testing
```

然后说：

```
@adversarial-testing 我的产品
```

两个独立 AI Agent 自动派生——开发者分析出摘要，对抗者执行攻击。

## ✨ 核心能力

| 维度 | 能力 |
|------|------|
| **攻击覆盖** | 18 类攻击，200+ 载荷（通用 6 类 + LLM 8 类 + 网络 + ML） |
| **安全机制** | 三层防御：Docker 沙箱 → 硬编码审批链（7 条规则）→ AI 约束 |
| **报告输出** | Markdown + SARIF（直通 GitHub Security）+ JSON + HTML |
| **CI/CD** | PR 自动触发测试，结果直接评论在 PR 上 |
| **严重度** | 类 CVSS 四维评分（可利用性 × 影响 × 敏感性 × 可修复性） |
| **审计** | 每一步可回溯重放的 JSON 日志 |
| **覆盖率** | 追踪哪些端点已测、未测及原因 |
| **趋势** | 跨轮次安全态势对比 |
| **多平台** | WorkBuddy / Cursor / Claude Code / 通用（TRAE, Codex, Copilot...） |
| **自动修复** | 安全漏洞自动生成 unified diff Patch |

## 📊 框架规模

38 个文件 · 5 篇理论指南 · 10 个模板 · 8 个载荷库 · 4 个平台适配器 · 7 条硬编码安全规则

---

## 📜 许可证

MIT © 2026 Kieran Howard
