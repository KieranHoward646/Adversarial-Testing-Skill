# Installing Adversarial Testing for Claude Code

## Quick Install

1. Copy `CLAUDE.md` to your project root:
   ```bash
   cp CLAUDE.md /path/to/your-project/
   ```

2. Copy the spec files:
   ```bash
   mkdir -p /path/to/your-project/docs/adversarial/
   cp ../../tools/developer-ai-spec.md /path/to/your-project/docs/adversarial/dev-spec.md
   cp ../../tools/adversarial-ai-spec.md /path/to/your-project/docs/adversarial/adv-spec.md
   cp ../../tools/templates/rules-of-engagement.md /path/to/your-project/docs/adversarial/roe.md
   ```

3. Start Claude Code in your project directory. The `CLAUDE.md` is auto-loaded.

## Recommended Workflow

Use TWO separate Claude Code sessions for true blind adversarial testing:

```
Terminal 1: claude          # Developer Claude (has source code context)
Terminal 2: claude          # Adversarial Claude (ONLY sees the summary)
```

This ensures the adversarial Claude cannot "cheat" by remembering implementation details.
