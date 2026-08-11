# Adversarial Testing Skill

> Multi-AI collaborative adversarial testing workflow — platform-agnostic core + per-platform adapters.  
> 多 AI 协作对抗测试工作流 —— 平台无关的核心规范 + 各平台适配器。

[English](#english) | [中文](#中文)

---

## English

### What is this?

A cross-platform adversarial testing framework with a **theory + tools closed loop**:

**Theory layer** — Read to understand the methodology:
- Complete attack taxonomy (general 6 categories + LLM 10 categories + network + ML)
- 4-phase methodology with threat modeling
- Real-world case studies (2023-2026)
- 30/60/90-day implementation roadmap

**Tools layer** — Use to execute tests:
- **Developer AI** — Analyzes the target product and produces a structured summary covering every entry point and test target
- **Adversarial AI** — Reads only the summary (zero knowledge of source code), safely executes tests, auto-fixes issues
- Templates for summaries, reports, and Rules of Engagement
- Attack payload libraries (SQL injection, XSS, LLM jailbreak, SSL mutation)

The adversarial AI is intentionally **blind** to the product internals — just like a real attacker.

### Choose Your Platform

This repo provides **one core spec** + **multiple platform adapters**. Pick the one that matches your AI coding assistant:

| Platform | Adapter | Multi-Agent? | Setup |
|----------|---------|:-----------:|-------|
| **WorkBuddy** | Root `SKILL.md` + `theory/` + `tools/` | ✅ Native Agent tool | Copy to `~/.workbuddy/skills/` |
| **Cursor** | `adapters/cursor/` | ✅ Two sessions | Copy `.cursorrules` + spec files to project |
| **Claude Code** | `adapters/claude-code/` | ✅ Two sessions | Copy `CLAUDE.md` + spec files to project |
| **TRAE IDE / Codex / Copilot / Windsurf / others** | `adapters/generic/` | ⚠️ Manual two-session | Use self-contained `DEVELOPER.md` and `ADVERSARIAL.md` |

> **Which should I choose?** If you use WorkBuddy, use the root files (native integration). For Cursor or Claude Code, use their adapters (they support file-based rules). For everything else, use the generic adapter (self-contained prompt files).

### Platform Details

#### WorkBuddy (Native)

Copy the entire repo to `~/.workbuddy/skills/adversarial-testing/`. Then just say:

```
@adversarial-testing <product>                        # Full pipeline (default, multi-agent)
@adversarial-testing sequential <product>             # With review gate
@adversarial-testing developer <product>              # Summary only
@adversarial-testing adversarial <summary-path>       # Test only
```

WorkBuddy natively spawns two independent Agent instances — true context isolation.

#### Cursor

See `adapters/cursor/INSTALL.md`. Quick setup:

```bash
cp adapters/cursor/.cursorrules /path/to/your-project/
mkdir -p /path/to/your-project/.cursor/rules/
cp references/for-developer-ai.md /path/to/your-project/.cursor/rules/dev-spec.md
cp references/for-adversarial-ai.md /path/to/your-project/.cursor/rules/adv-spec.md
```

Use **two Cursor chat sessions** for true blind testing.

#### Claude Code

See `adapters/claude-code/INSTALL.md`. Quick setup:

```bash
cp adapters/claude-code/CLAUDE.md /path/to/your-project/
mkdir -p /path/to/your-project/docs/adversarial/
cp references/for-developer-ai.md /path/to/your-project/docs/adversarial/dev-spec.md
cp references/for-adversarial-ai.md /path/to/your-project/docs/adversarial/adv-spec.md
```

Use **two Claude Code terminal sessions** for true blind testing.

#### Generic (TRAE IDE, Codex, Copilot, Windsurf, etc.)

See `adapters/generic/INSTALL.md`. Uses two **self-contained prompt files** — no external dependencies:

1. Open AI assistant → paste `adapters/generic/DEVELOPER.md` as system prompt → generate summary
2. Open a **NEW session** → paste `adapters/generic/ADVERSARIAL.md` as system prompt → execute tests

The generic files embed the full spec inline, so they work on any platform without needing file path references.

### Safety Guarantees (all platforms)

- ❌ No `DROP`, `TRUNCATE`, or unconditional `DELETE`
- ❌ No modification of production data or config files
- ❌ No real external requests (emails, SMS, payments)
- ❌ Injection testing stops immediately if a real vulnerability is found
- ✅ All destructive operations require explicit safe-boundary declarations

### File Structure

```
adversarial-testing/
├── SKILL.md                           # WorkBuddy orchestration (native Agent spawning)
├── README.md                          # This file
├── LICENSE                            # MIT
│
├── theory/                            # THEORY — knowledge base & methodology
│   ├── 01-overview.md                 #   What & why of adversarial testing
│   ├── 02-attack-taxonomy.md          #   Complete attack classification (A/B/C/D)
│   ├── 03-methodology.md              #   4-phase workflow + threat modeling
│   ├── 04-case-studies.md             #   Real incidents 2023-2026
│   └── 05-roadmap.md                  #   30/60/90-day plan + maturity model
│
├── tools/                             # TOOLS — executable specs & payloads
│   ├── developer-ai-spec.md           #   Developer AI output format (9 sections)
│   ├── adversarial-ai-spec.md         #   Adversarial AI safety & testing (4 phases)
│   ├── templates/
│   │   ├── summary-template.md        #   Blank summary scaffold
│   │   ├── report-template.md         #   Blank report scaffold
│   │   └── rules-of-engagement.md     #   Pre-test boundary definition
│   └── payloads/
│       ├── injection-payloads.md      #   SQL/XSS/Command/SSTI (A2)
│       ├── llm-payloads.md            #   Prompt injection/jailbreak (B1-B8)
│       └── ssl-payloads.md            #   Certificate mutation strategies (C1-C2)
│
└── adapters/                          # Platform adapters
    ├── cursor/                        #   Cursor IDE
    ├── claude-code/                   #   Claude Code
    └── generic/                       #   Fallback (TRAE, Codex, Copilot, etc.)
```

---

## 中文

### 这是什么？

一个跨平台的对抗测试框架，形成**理论 + 工具闭环**：

**理论层** — 阅读以理解方法论：
- 完整攻击分类体系（通用 6 类 + LLM 10 类 + 网络协议 + ML）
- 四阶段方法论含威胁建模
- 真实案例时间线（2023-2026）
- 30/60/90 天实施路线图

**工具层** — 用于执行测试：
- **开发者 AI** — 分析目标产品，输出覆盖所有入口和测试目标的结构化摘要
- **对抗测试 AI** — 仅阅读摘要（对源码零知识），安全执行测试并自动修复
- 模板：摘要、报告、交战规则
- 攻击载荷库：SQL 注入、XSS、LLM 越狱、SSL 变异

### 选择你的平台

本仓库提供**一套核心规范** + **多个平台适配器**。根据你使用的 AI 编程助手选择：

| 平台 | 适配器 | 多Agent？ | 安装方式 |
|------|--------|:------:|------|
| **WorkBuddy** | 根目录 `SKILL.md` + `theory/` + `tools/` | ✅ 原生 Agent 工具 | 复制到 `~/.workbuddy/skills/` |
| **Cursor** | `adapters/cursor/` | ✅ 双会话 | 复制 `.cursorrules` + 规范文件到项目 |
| **Claude Code** | `adapters/claude-code/` | ✅ 双会话 | 复制 `CLAUDE.md` + 规范文件到项目 |
| **TRAE IDE / Codex / Copilot / Windsurf 等** | `adapters/generic/` | ⚠️ 手动双会话 | 使用自包含的 `DEVELOPER.md` 和 `ADVERSARIAL.md` |

> **我该选哪个？** WorkBuddy 用户直接用根目录文件（原生集成）。Cursor 或 Claude Code 用户用对应适配器（支持文件级规则）。其他平台用通用适配器（自包含提示词文件）。

### 平台详情

#### WorkBuddy（原生）

将整个仓库复制到 `~/.workbuddy/skills/adversarial-testing/`。然后直接说：

```
@adversarial-testing <产品>                        # 全流水线（默认，多Agent协作）
@adversarial-testing sequential <产品>             # 带审核关卡
@adversarial-testing developer <产品>              # 仅出总结
@adversarial-testing adversarial <摘要路径>         # 仅执行测试
```

WorkBuddy 原生支持派生两个独立 Agent 实例 —— 真正的上下文隔离。

#### Cursor

详见 `adapters/cursor/INSTALL.md`。快速安装：

```bash
cp adapters/cursor/.cursorrules /path/to/your-project/
mkdir -p /path/to/your-project/.cursor/rules/
cp tools/developer-ai-spec.md /path/to/your-project/.cursor/rules/dev-spec.md
cp tools/adversarial-ai-spec.md /path/to/your-project/.cursor/rules/adv-spec.md
```

建议使用**两个 Cursor 会话**实现真正的盲测。

#### Claude Code

详见 `adapters/claude-code/INSTALL.md`。快速安装：

```bash
cp adapters/claude-code/CLAUDE.md /path/to/your-project/
mkdir -p /path/to/your-project/docs/adversarial/
cp tools/developer-ai-spec.md /path/to/your-project/docs/adversarial/dev-spec.md
cp tools/adversarial-ai-spec.md /path/to/your-project/docs/adversarial/adv-spec.md
```

建议使用**两个 Claude Code 终端会话**实现真正的盲测。

#### 通用适配器（TRAE IDE、Codex、Copilot、Windsurf 等）

详见 `adapters/generic/INSTALL.md`。使用两个**自包含提示词文件** —— 无外部依赖：

1. 打开 AI 助手 → 将 `adapters/generic/DEVELOPER.md` 作为系统提示词粘贴 → 生成摘要
2. 打开**新会话** → 将 `adapters/generic/ADVERSARIAL.md` 作为系统提示词粘贴 → 执行测试

通用适配器将完整规范内嵌在文件中，无需依赖外部文件路径，适配任何平台。

### 安全保障（所有平台通用）

- ❌ 不执行 `DROP`、`TRUNCATE`、无条件 `DELETE`
- ❌ 不修改生产数据或配置文件
- ❌ 不发送真实外部请求（邮件、短信、支付）
- ❌ 注入测试发现真实漏洞后立即停止同类测试
- ✅ 所有破坏性操作须在摘要中明确声明安全边界

### 文件结构

```
adversarial-testing/
├── SKILL.md                           # WorkBuddy 编排层（原生 Agent 派生）
├── README.md                          # 本文件
├── LICENSE                            # MIT
│
├── theory/                            # 理论 — 知识库与方法论
│   ├── 01-overview.md                 #   什么是对抗测试，为什么重要
│   ├── 02-attack-taxonomy.md          #   完整攻击分类（A/B/C/D 四大类）
│   ├── 03-methodology.md              #   四阶段方法论 + 威胁建模
│   ├── 04-case-studies.md             #   真实安全事件（2023-2026）
│   └── 05-roadmap.md                  #   30/60/90 天计划 + 成熟度模型
│
├── tools/                             # 工具 — 可执行规范与载荷库
│   ├── developer-ai-spec.md           #   开发者 AI 输出规范（9 章节）
│   ├── adversarial-ai-spec.md         #   对抗测试 AI 安全协议（4 阶段）
│   ├── templates/
│   │   ├── summary-template.md        #   摘要空白模板
│   │   ├── report-template.md         #   报告空白模板
│   │   └── rules-of-engagement.md     #   交战规则模板
│   └── payloads/
│       ├── injection-payloads.md      #   SQL/XSS/命令注入载荷（A2）
│       ├── llm-payloads.md            #   提示词注入/越狱载荷（B1-B8）
│       └── ssl-payloads.md            #   证书变异策略（C1-C2）
│
└── adapters/                          # 平台适配器
    ├── cursor/                        #   Cursor IDE
    ├── claude-code/                   #   Claude Code
    └── generic/                       #   通用兜底（TRAE/Codex/Copilot 等）
```

---

## License

MIT © 2026 Kieran Howard
