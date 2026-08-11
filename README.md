# Adversarial Testing Skill

> Multi-AI collaborative adversarial testing workflow — platform-agnostic core + per-platform adapters.  
> 多 AI 协作对抗测试工作流 —— 平台无关的核心规范 + 各平台适配器。

[English](#english) | [中文](#中文)

---

## English

### What is this?

A cross-platform adversarial testing framework where two AI agents collaborate:

1. **Developer AI** — Analyzes the target product and produces a structured summary covering every entry point, input constraint, error scenario, and test target across six categories (boundary, injection, auth, concurrency, exception, business logic).
2. **Adversarial AI** — Reads only the summary (zero knowledge of source code), safely executes destructive tests, auto-fixes issues within safe boundaries, and produces a comprehensive test report.

The adversarial AI is intentionally **blind** to the product internals — just like a real attacker.

### Choose Your Platform

This repo provides **one core spec** + **multiple platform adapters**. Pick the one that matches your AI coding assistant:

| Platform | Adapter | Multi-Agent? | Setup |
|----------|---------|:-----------:|-------|
| **WorkBuddy** | Root `SKILL.md` + `references/` | ✅ Native Agent tool | Copy to `~/.workbuddy/skills/` |
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
├── references/                        # Universal core specs (shared by all adapters)
│   ├── for-developer-ai.md            # Developer AI output format (9 sections)
│   └── for-adversarial-ai.md          # Adversarial AI safety & testing protocol (4 phases)
└── adapters/
    ├── cursor/                        # Cursor IDE adapter
    │   ├── .cursorrules               # Cursor rules (orchestration)
    │   └── INSTALL.md                 # Setup guide
    ├── claude-code/                   # Claude Code adapter
    │   ├── CLAUDE.md                  # Claude Code instructions
    │   └── INSTALL.md                 # Setup guide
    └── generic/                       # Fallback for any AI assistant
        ├── DEVELOPER.md               # Self-contained developer AI prompt
        ├── ADVERSARIAL.md             # Self-contained adversarial AI prompt
        └── INSTALL.md                 # Setup guide
```

---

## 中文

### 这是什么？

一个跨平台的对抗测试框架，双 AI 协作：

1. **开发者 AI** — 分析目标产品，输出结构化摘要，覆盖所有入口、输入约束、错误场景，以及六大类测试目标（边界 / 注入 / 权限 / 并发 / 异常 / 业务逻辑）。
2. **对抗测试 AI** — 仅阅读摘要（对源码零知识），安全执行破坏性测试，在安全边界内自动修复问题，输出完整测试报告。

对抗测试 AI 故意被设计为对产品内部实现**盲测** —— 模拟真实攻击场景。

### 选择你的平台

本仓库提供**一套核心规范** + **多个平台适配器**。根据你使用的 AI 编程助手选择：

| 平台 | 适配器 | 多Agent？ | 安装方式 |
|------|--------|:------:|------|
| **WorkBuddy** | 根目录 `SKILL.md` + `references/` | ✅ 原生 Agent 工具 | 复制到 `~/.workbuddy/skills/` |
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
cp references/for-developer-ai.md /path/to/your-project/.cursor/rules/dev-spec.md
cp references/for-adversarial-ai.md /path/to/your-project/.cursor/rules/adv-spec.md
```

建议使用**两个 Cursor 会话**实现真正的盲测。

#### Claude Code

详见 `adapters/claude-code/INSTALL.md`。快速安装：

```bash
cp adapters/claude-code/CLAUDE.md /path/to/your-project/
mkdir -p /path/to/your-project/docs/adversarial/
cp references/for-developer-ai.md /path/to/your-project/docs/adversarial/dev-spec.md
cp references/for-adversarial-ai.md /path/to/your-project/docs/adversarial/adv-spec.md
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
├── references/                        # 通用核心规范（所有适配器共享）
│   ├── for-developer-ai.md            # 开发者 AI 输出格式（9 章节）
│   └── for-adversarial-ai.md          # 对抗测试 AI 安全与测试协议（4 阶段）
└── adapters/
    ├── cursor/                        # Cursor IDE 适配器
    │   ├── .cursorrules               # Cursor 规则（编排）
    │   └── INSTALL.md                 # 安装指南
    ├── claude-code/                   # Claude Code 适配器
    │   ├── CLAUDE.md                  # Claude Code 指令
    │   └── INSTALL.md                 # 安装指南
    └── generic/                       # 通用兜底（适配任意 AI 助手）
        ├── DEVELOPER.md               # 自包含开发者 AI 提示词
        ├── ADVERSARIAL.md             # 自包含对抗测试 AI 提示词
        └── INSTALL.md                 # 安装指南
```

---

## License

MIT © 2026 Kieran Howard
