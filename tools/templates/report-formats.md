# 多格式报告输出 — SARIF / JSON / HTML

> 对抗测试 AI 在完成测试后，除了生成 Markdown 报告，还应输出机器可消费的格式。

---

## SARIF 输出（GitHub Code Scanning 集成）

SARIF (Static Analysis Results Interchange Format) 是 GitHub Code Scanning 的标准格式。输出 SARIF 后，发现直接出现在 PR 的 Security 标签页。

### 最小有效 SARIF 结构

```json
{
  "$schema": "https://raw.githubusercontent.com/oasis-tcs/sarif-spec/master/Schemata/sarif-schema-2.1.0.json",
  "version": "2.1.0",
  "runs": [
    {
      "tool": {
        "driver": {
          "name": "Adversarial Testing Skill",
          "version": "2.1",
          "informationUri": "https://github.com/kieranhoward/adversarial-testing-skill",
          "rules": []
        }
      },
      "results": []
    }
  ]
}
```

### 规则定义（每条发现 = 一条规则 + 一个结果）

```json
{
  "id": "ADV-I-001",
  "shortDescription": {
    "text": "SQL Injection via email parameter"
  },
  "fullDescription": {
    "text": "The email parameter on POST /api/login is vulnerable to SQL injection. The payload ' OR '1'='1 was executed."
  },
  "helpUri": "https://github.com/kieranhoward/adversarial-testing-skill/blob/main/theory/02-attack-taxonomy.md",
  "properties": {
    "severity": "critical",
    "severity_score": 9,
    "exploitability": 4,
    "impact": 3,
    "data_sensitivity": 2,
    "fixability": 0,
    "category": "A2-injection",
    "cwe": "CWE-89"
  }
}
```

### 结果定义

```json
{
  "ruleId": "ADV-I-001",
  "message": {
    "text": "SQL Injection detected on POST /api/login. Payload: ' OR '1'='1 returned all user rows."
  },
  "locations": [
    {
      "physicalLocation": {
        "artifactLocation": {
          "uri": "src/auth/login.js"
        },
        "region": {
          "startLine": 45,
          "endLine": 52
        }
      }
    }
  ],
  "level": "error",
  "properties": {
    "test_id": "I-001",
    "fix_applied": true,
    "fix_diff": "diff content here..."
  }
}
```

### 严重度到 SARIF Level 映射

| 我们的严重度 | SARIF level | GitHub 显示 |
|:-----------:|-------------|------------|
| Critical (8-10) | `error` | 🔴 阻断 PR |
| High (6-7) | `error` | 🔴 阻断 PR（可配置） |
| Medium (4-5) | `warning` | 🟡 警告 |
| Low (1-3) | `warning` | 🟡 警告 |
| Info (0) | `note` | ⚪ 信息 |

### 在 CI 中使用

```yaml
# 在 .github/workflows 中追加：
- name: Upload SARIF
  uses: github/codeql-action/upload-sarif@v3
  with:
    sarif_file: adversarial-test-report.sarif
    category: adversarial-testing
```

---

## JSON 机器可消费格式

用于工具链消费（SOAR/SIEM/自定义 Dashboard）。结构复用审计日志的 JSON Schema（`audit-log-schema.json`）。

输出文件：`adversarial-test-report-<product>.json`

```json
{
  "report_version": "2.1",
  "generated_at": "2026-08-12T17:00:00Z",
  "product": { "name": "xxx", "version": "v1.0.0" },
  "summary": {
    "total": 60,
    "passed": 52,
    "failed": 8,
    "skipped": 0,
    "pass_rate": 86.7
  },
  "severity_distribution": {
    "critical": 2,
    "high": 1,
    "medium": 3,
    "low": 2,
    "info": 0
  },
  "coverage": {
    "total_entry_points": 12,
    "covered": 10,
    "rate": 83.3
  },
  "findings": [
    {
      "id": "I-001",
      "category": "A2-injection",
      "title": "SQL Injection via email parameter",
      "severity": "critical",
      "score": 9,
      "status": "fixed",
      "endpoint": "POST /api/login",
      "payload": "' OR '1'='1",
      "fix_file": "src/auth/login.js",
      "fix_lines": "45-52"
    }
  ],
  "audit_log": "adversarial-test-audit-xxx.json"
}
```

---

## HTML 可视化报告

用于人工审阅的可读报告。生成自包含 HTML 文件。

输出文件：`adversarial-test-report-<product>.html`

```html
<!DOCTYPE html>
<html lang="zh">
<head>
  <meta charset="UTF-8">
  <title>对抗测试报告 — [产品名]</title>
  <style>
    :root { --critical: #dc2626; --high: #ea580c; --medium: #ca8a04; --low: #16a34a; --info: #6b7280; }
    body { font-family: system-ui; max-width: 900px; margin: 0 auto; padding: 2rem; }
    .score-card { border: 1px solid #e5e7eb; border-radius: 8px; padding: 1rem; margin: 0.5rem; display: inline-block; min-width: 100px; text-align: center; }
    .score-card h3 { margin: 0; font-size: 2rem; }
    .critical { color: var(--critical); } .high { color: var(--high); }
    table { width: 100%; border-collapse: collapse; margin: 1rem 0; }
    th, td { border: 1px solid #e5e7eb; padding: 0.5rem; text-align: left; }
    th { background: #f3f4f6; }
  </style>
</head>
<body>
  <h1>🛡️ 对抗测试报告</h1>
  <!-- Summary cards, severity distribution, findings table, coverage chart -->
</body>
</html>
```

---

## 输出规范

测试完成后，按以下顺序生成报告：

1. **Markdown** (`report-<product>.md`) — 主报告，人工审阅
2. **SARIF** (`report-<product>.sarif`) — GitHub Security Tab 集成
3. **JSON** (`report-<product>.json`) — 机器消费
4. **HTML** (`report-<product>.html`) — 可视化审阅
5. **审计日志** (`audit-<product>.json`) — 可回溯重放
