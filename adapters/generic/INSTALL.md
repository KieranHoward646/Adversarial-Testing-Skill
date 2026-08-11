# Installing Adversarial Testing — Generic Adapter

For AI assistants without native multi-agent support (TRAE IDE, GitHub Copilot, Codex, Windsurf, etc.).

## Quick Start

This adapter uses **two self-contained prompt files**. No external dependencies.

### Step 1: Developer Phase

1. Open your AI coding assistant in the target project
2. Copy the **entire content** of `DEVELOPER.md` as the system prompt / instruction
3. Tell the AI: "Produce an adversarial testing summary for `<product/feature>`"
4. Save the output as `adversarial-test-summary-<product>.md`

### Step 2: Adversarial Phase (NEW session!)

1. **Close the current session** — this is critical for true blind testing
2. Open a **new** AI session (the AI must NOT have source code context)
3. Copy the **entire content** of `ADVERSARIAL.md` as the system prompt
4. Provide the summary file from Step 1
5. Tell the AI: "Execute adversarial tests against this summary"

### Why Two Sessions?

The adversarial AI should have ZERO knowledge of internal implementation — just like a real attacker. If both phases run in the same session, the AI may subconsciously "go easy" on code it remembers writing.

## Platform Notes

| Platform | How to set system prompt |
|----------|-------------------------|
| **TRAE IDE** | Paste into `.trae/rules/` or use `/instructions` |
| **GitHub Copilot** | Paste into `.github/copilot-instructions.md` for Phase 1, use inline chat for Phase 2 |
| **Codex CLI** | `codex exec --instructions DEVELOPER.md` |
| **Windsurf** | Paste into `.windsurfrules` |
| **Others** | Paste directly into the chat input prefixed with "System:" or use the platform's instruction mechanism |

## File Layout

```
your-project/
├── adversarial-test-summary-<product>.md    ← output of Step 1
└── adversarial-test-report-<product>.md     ← output of Step 2
```

The `DEVELOPER.md` and `ADVERSARIAL.md` files can stay wherever you cloned the repo — they don't need to be in the project directory.
