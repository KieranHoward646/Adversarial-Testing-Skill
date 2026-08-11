# CLAUDE.md — Adversarial Testing Workflow

## Overview

This file enables adversarial testing in Claude Code. Two Claude instances collaborate:
1. **Developer Claude** — analyzes the product, produces a structured test summary
2. **Adversarial Claude** — reads only the summary, executes tests, auto-fixes

## Setup

Copy these files to your project:
- This `CLAUDE.md` → project root
- `references/for-developer-ai.md` → `docs/adversarial/dev-spec.md`
- `references/for-adversarial-ai.md` → `docs/adversarial/adv-spec.md`

## Usage

### Full Pipeline (two Claude Code sessions recommended)

**Session 1 — Developer Claude**:
```
You are the Developer AI for adversarial testing. 
1. Read docs/adversarial/dev-spec.md for the output format.
2. Analyze the target product thoroughly: read source code, map all entry points, 
   document input constraints, error scenarios, and test targets.
3. Produce a summary following dev-spec.md EXACTLY.
4. Save to adversarial-test-summary-<product>.md.
5. When done, tell the user to start a new Claude Code session for Phase 2.
```

**Session 2 — Adversarial Claude** (NEW session, NO source code context):
```
You are the Adversarial AI for adversarial testing. You have NO knowledge of the 
product internals — you only have the summary file.

1. Read docs/adversarial/adv-spec.md for safety protocols and testing procedures.
2. Read adversarial-test-summary-<product>.md.
3. Execute the 4-phase workflow from adv-spec.md:
   - Safety verification
   - Tests in order: boundary → injection → auth → concurrency → exception → business logic
   - Fix safe issues
   - Produce report: adversarial-test-report-<product>.md
```

### Single-Session (Sequential with gate)

In one Claude Code session:
```
"对抗测试 <product> — sequential mode"

Claude will: Phase 1 → present summary → WAIT for your "go" → Phase 2
```

Note: single-session mode means the same Claude has both source code and testing context, which is less adversarial than the two-session approach.

## Safety Rules

Same as the core spec — NEVER: DROP, TRUNCATE, unconditional DELETE, modify production data, send real external requests. Injection tests that find real vulnerabilities must stop that category immediately.

## References

Detailed specs live in `docs/adversarial/`:
- `dev-spec.md` — Developer AI output format (9 sections)
- `adv-spec.md` — Adversarial AI safety & testing protocol (4 phases)
