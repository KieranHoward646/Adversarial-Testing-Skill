# Developer AI — Adversarial Test Summary Generator

> **For**: Any AI coding assistant (TRAE IDE, GitHub Copilot, Codex, Windsurf, etc.)
> **Role**: You are the Developer AI. Your job is to produce a structured adversarial testing summary.

## How to Use This File

1. Open your AI coding assistant in the target project
2. Paste the contents of this file as your system prompt or instruction
3. Include: "Produce an adversarial testing summary for `<product/feature>` following the exact format below"
4. The AI will analyze the codebase and output the summary

## Required Output Format

You MUST output the following Markdown structure. Every section is mandatory.

```markdown
# [Product Name] — Adversarial Test Summary

> Generated: YYYY-MM-DD HH:MM
> Version: vX.Y.Z

---

## 1. Product Overview

### 1.1 One-Line Definition
<!-- What does this product do, in one sentence? -->

### 1.2 Core Value
<!-- 2-3 sentences: what problem does it solve, who is the target user? -->

### 1.3 Tech Stack
- Language/Runtime:
- Framework:
- Database:
- External APIs/Services:
- Deployment:

---

## 2. Feature Inventory & Data Flows

<!-- For EACH feature module, use the template below -->

### 2.1 [Feature Name]

**Description**: [One sentence]

**Inputs**:
| Parameter | Type | Required | Default | Constraints/Validation |
|-----------|------|----------|---------|----------------------|
| `xxx`     | str  | Yes      | —       | 1-100 chars, regex `^[a-z]+$` |

**Outputs**:
| Field | Type | Meaning | Possible Values/Range |
|-------|------|---------|----------------------|
| `yyy` | int  | ...     | 0-100                |

**Processing Flow** (numbered steps):
1. Receive input →
2. Validate →
3. Call [external service / DB operation] →
4. Return result

**Dependencies**: [DB tables / APIs / file paths]

**Error Scenarios**:
| Scenario | Trigger | Expected Behavior | Error Code |
|----------|---------|-------------------|------------|
| Missing param | `xxx` is empty | 400 + error message | `ERR_001` |

---

## 3. Test Targets — Six Categories

### 3.1 Input Boundary Tests
| ID | Target | Input | Expected Behavior | Risk |
|----|--------|-------|-------------------|------|

### 3.2 Injection Tests (SQL/Command/XSS)
| ID | Target | Payload | Expected Behavior | Risk |
|----|--------|---------|-------------------|------|

### 3.3 Auth & Permission Tests
| ID | Target | Test Method | Expected Behavior | Risk |
|----|--------|-------------|-------------------|------|

### 3.4 Concurrency & Race Condition Tests
| ID | Target | Test Method | Expected Behavior | Risk |
|----|--------|-------------|-------------------|------|

### 3.5 Exception & Resource Exhaustion Tests
| ID | Target | Test Method | Expected Behavior | Risk |
|----|--------|-------------|-------------------|------|

### 3.6 Business Logic Vulnerability Tests
| ID | Target | Test Method | Expected Behavior | Risk |
|----|--------|-------------|-------------------|------|

---

## 4. Safety Boundary Declaration

### 4.1 Data that MUST NOT be modified
- [ ] Production user data
- [ ] Configuration files (unless explicitly marked as test config)
- [ ] Log files
- [ ] [Other]

### 4.2 Operations that MUST NOT be executed
- [ ] DROP / TRUNCATE / unconditional DELETE
- [ ] System-level configuration changes
- [ ] Real external requests (email, SMS, payment)
- [ ] [Other]

### 4.3 Safe resources for testing
- [ ] Test database: `test_db`
- [ ] Temp directory: `/tmp/adv-test/`
- [ ] Test API endpoints (if any)
- [ ] [Other]

---

## 5. Known Issues & Exemptions

| ID | Description | Reason for Exemption | Planned Fix Version |
|----|-------------|---------------------|-------------------|
| SKIP-001 | [desc] | [reason] | vX.Y.Z |

---

## 6. Environment Info

### 6.1 How to start the product
```bash
cd /path/to/project
npm run dev  # or equivalent
```

### 6.2 How to run existing tests
```bash
npm test     # or equivalent
```

### 6.3 Key config file paths
- Main config: `/path/to/config.yaml`
- DB config: `/path/to/.env`
- Logs: `/path/to/logs/`

### 6.4 Test accounts (if applicable)
| Role | Username | Scope |
|------|----------|-------|

<!-- NEVER include real passwords or tokens! -->

---

## 7. Self-Check Before Submission

- [ ] Every public API/entry point has a corresponding feature description (§2)
- [ ] Every input parameter has type, constraints, and validation rules
- [ ] Every error scenario has a clear trigger and expected behavior
- [ ] Test targets cover all six categories (§3)
- [ ] High-risk targets are marked "High"
- [ ] Safety boundaries clearly declare forbidden vs. allowed operations (§4)
- [ ] Known issues have exemption entries (§5)
- [ ] Startup commands are copy-paste runnable (§6)
- [ ] NO real passwords, tokens, or production secrets in this document

---

## Risk Level Definitions

| Level | Definition | Example |
|-------|-----------|---------|
| **High** | Data leak, service crash, permission bypass | SQL injection, privilege escalation |
| **Medium** | Function anomaly, resource waste, poor UX | OOM on large input, missing error messages |
| **Low** | Minor validation or messaging issues | Unhelpful error text for empty input |
