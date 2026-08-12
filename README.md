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

```
你 → "对抗测试 我的登录API"
         │
         ▼
  ┌──────────────┐    ┌──────────────────┐
  │ 开发者 AI     │───▶│ 结构化摘要         │   只有摘要
  │（阅读源码）    │    │                  │───▶被传递过去
  └──────────────┘    └──────────────────┘
                              │
                              ▼
                       ┌──────────────┐
                       │ 对抗测试 AI   │   对源码
                       │（盲测）       │   一无所知
                       └──────┬───────┘
                              │
              ┌───────────────┼───────────────┐
              ▼               ▼               ▼
         🛡️ 执行测试     🔧 自动修复      📊 输出报告
         (8 大攻击      (diff Patch)    (SARIF+JSON
          类别)                          +HTML+MD)
```

**对抗测试 AI 故意被设计为对源码盲测**——和真实攻击者一样，只能从接口行为推断系统弱点。

### 为什么需要这个框架？

AI 系统有全新的攻击面：**自然语言**。2025 年全球 Prompt Injection 攻击损失约 $2.3B。传统测试工具解析不了自然语言攻击——这个框架填补了空白。**用 AI 测试 AI。**

---

## 🚀 快速开始

### WorkBuddy（30 秒）

```bash
git clone git@gitee.com:kieranhoward/adversarial-testing-skill.git \
  ~/.workbuddy/skills/adversarial-testing
```

然后直接说：

```
@adversarial-testing 我的产品        # 全流水线（默认，双 Agent 协作）
@adversarial-testing sequential 我的产品  # 先审核摘要再放行测试
@adversarial-testing developer 我的产品   # 仅出摘要
@adversarial-testing adversarial 摘要.md  # 仅执行测试
```

WorkBuddy 原生派生两个独立 Agent —— 真正的上下文隔离。

### 其他平台

```bash
git clone git@gitee.com:kieranhoward/adversarial-testing-skill.git
cd adversarial-testing-skill

# Cursor
cp adapters/cursor/.cursorrules /你的项目/
cp tools/developer-ai-spec.md /你的项目/.cursor/rules/dev-spec.md
cp tools/adversarial-ai-spec.md /你的项目/.cursor/rules/adv-spec.md

# Claude Code
cp adapters/claude-code/CLAUDE.md /你的项目/

# 任意 AI 助手（TRAE, Codex, Copilot, Windsurf...）
# 使用 adapters/generic/ 下的自包含提示词文件
```

---

## 🏗 架构

```
adversarial-testing/
│
├── 📚 theory/               "为什么 & 怎么做"
│   ├── 01-overview           核心概念
│   ├── 02-attack-taxonomy    四大类攻击体系 (A/B/C/D)
│   ├── 03-methodology        四阶段工作流
│   ├── 04-case-studies       2023-2026 真实安全事件
│   └── 05-roadmap            30/60/90 天实施路线图
│
├── 🔧 tools/                "执行引擎"
│   ├── developer-ai-spec     开发者 AI 规范：如何产出摘要
│   ├── adversarial-ai-spec   对抗测试 AI 规范：如何安全测试
│   │
│   ├── templates/            10 个实战模板
│   │   ├── summary            摘要 + 报告结构模板
│   │   ├── report
│   │   ├── rules-of-engagement  交战规则（法律/安全边界）
│   │   ├── severity-scoring     类 CVSS 0-10 评分体系
│   │   ├── audit-log-schema     可重放 JSON 审计日志
│   │   ├── report-formats       SARIF / JSON / HTML 多格式
│   │   ├── operation-approval   7 条硬编码安全规则引擎
│   │   ├── patch-generation     自动修复 diff 生成
│   │   ├── docker-sandbox       容器隔离模板
│   │   └── trend-report         历史对比趋势报告
│   │
│   └── payloads/             8 个攻击载荷库
│       ├── injection           SQL/XSS/SSTI/命令注入
│       ├── jwt                 JWT 算法混淆、声明篡改
│       ├── ssrf                云元数据探测、8 种绕过手法
│       ├── deserialization     Pickle/Java/PHP/Node/.NET
│       ├── file-upload         Web Shell、Zip Slip、魔术字节
│       ├── llm                 越狱、提示词注入、多模态攻击
│       ├── ssl                 证书变异 + 差分测试
│       └── tech-stack-mapping  技术栈 → 载荷智能匹配
│
├── 🔌 adapters/             "跨平台适配"
│   ├── cursor/               .cursorrules + 规范文件
│   ├── claude-code/           CLAUDE.md
│   └── generic/               自包含提示词（任意 AI 可用）
│
├── ⚙️ .github/workflows/    "CI/CD"
│   └── adversarial-test.yml  PR 触发 → 自动测试 → PR 评论
│
├── 👥 community/             "生态"
│   └── test-case-template    提交你自己的攻击用例
│
└── 🤝 CONTRIBUTING.md        贡献指南
```

---

## ✨ 核心能力

### 🎯 攻击覆盖

| 类别 | 范围 | 载荷数 |
|------|------|:------:|
| **A — 通用软件** | 6 类（边界、注入、权限、并发、异常、业务逻辑） | 100+ |
| **B — LLM/AI** | 8 类（提示词注入、越狱、系统泄露、投毒、Agent 攻击、多模态...） | 50+ |
| **C — 网络协议** | SSL/TLS 证书验证、协议状态机 | 30+ |
| **D — ML 模型** | 逃避攻击、投毒、模型窃取 | 20+ |

### 🛡️ 三层安全防线

不靠一句"注意安全"的 prompt 约束——而是真正的纵深防御：

```
第一层：Docker 沙箱    ← OS 级隔离（只读文件系统、移除特权）
第二层：硬编码审批链    ← 7 条规则引擎（DROP/SYSTEM/PROD = 直接拒绝）
第三层：AI 指令约束     ← 自然语言护栏（最后一道防线）
```

### 📊 企业级报告

- **SARIF** → 一行 CI 配置，直通 GitHub Security Tab
- **JSON** → 机器消费（SOAR/SIEM/自定义 Dashboard）
- **Markdown** → 人类审阅
- **HTML** → 可视化面板
- **严重度评分** → 四维度 0-10 分（可利用性 × 影响范围 × 数据敏感性 × 可修复性）
- **审计日志** → 每一步可回溯重放
- **覆盖率追踪** → 哪些端点已测、哪些遗漏、遗漏原因
- **趋势报告** → 跨轮次安全态势变化（新增/修复/复现）

### 🔄 CI/CD 原生

```yaml
# .github/workflows/adversarial-test.yml
# PR 提交 → 自动触发对抗测试 → 结果作为 PR 评论
# 发现 Critical 漏洞？自动上传 SARIF 到 GitHub Security Tab
```

### 🌍 跨平台

| 平台 | 多 Agent？ | 安装 |
|------|:------:|------|
| **WorkBuddy** | ✅ 原生 | `@adversarial-testing` 一句话 |
| **Cursor** | ✅ 双会话 | 复制规则文件 |
| **Claude Code** | ✅ 双终端 | 复制 CLAUDE.md |
| **任意 AI** | ⚠️ 手动 | 复制自包含提示词 |

---

## 📊 框架规模

| 指标 | 数量 |
|------|:----:|
| 总文件数 | 38 |
| 理论指南 | 5 篇 |
| 可执行模板 | 10 个 |
| 攻击载荷库 | 8 个 |
| 平台适配器 | 4 个 |
| 攻击分类覆盖 | 18 类 |
| 硬编码安全规则 | 7 条 |
| 真实案例 | 15+ |

---

## 🗺️ 从哪里开始？

| 你是... | 从这里开始 |
|--------|-----------|
| **新用户** | `theory/01-overview.md` → 理解"为什么" |
| **开发者想测自己的产品** | `@adversarial-testing 我的产品` |
| **安全研究员** | `theory/02-attack-taxonomy.md` + `tools/payloads/` |
| **团队负责人** | `theory/05-roadmap.md` → 30/60/90 天计划 |
| **贡献者** | `CONTRIBUTING.md` + `community/test-case-template.md` |

---

## 📜 许可证

MIT © 2026 Kieran Howard

---

<p align="center">
  <sub>Built with • anger at insecure software • respect for real red teams • and a lot of Markdown</sub>
</p>

