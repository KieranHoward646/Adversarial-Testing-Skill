# Installing Adversarial Testing for Cursor

## Quick Install

1. Copy `.cursorrules` to your project root:
   ```bash
   cp .cursorrules /path/to/your-project/
   ```

2. Copy spec and payload files into `.cursor/rules/`:
   ```bash
   mkdir -p /path/to/your-project/.cursor/rules/
   cp ../../tools/developer-ai-spec.md /path/to/your-project/.cursor/rules/dev-spec.md
   cp ../../tools/adversarial-ai-spec.md /path/to/your-project/.cursor/rules/adv-spec.md
   cp ../../tools/templates/rules-of-engagement.md /path/to/your-project/.cursor/rules/roe.md
   ```

3. In Cursor, the rules are automatically loaded. You can now say "对抗测试 <your product>" to start.

## Two-Session Workflow (Recommended)

For true blind adversarial testing, use TWO separate Cursor chat sessions:

| Session | Role | Context |
|---------|------|---------|
| Session 1 | Developer AI | Full source code access |
| Session 2 | Adversarial AI | Only the summary file (no source code) |

This ensures the adversarial AI has ZERO knowledge of internal implementation — just like a real attacker.

## File Layout After Install

```
your-project/
├── .cursorrules
├── .cursor/
│   └── rules/
│       ├── dev-spec.md
│       └── adv-spec.md
```
