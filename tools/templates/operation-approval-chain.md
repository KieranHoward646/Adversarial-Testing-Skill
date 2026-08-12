# 操作审批链 — 硬编码安全拦截规则

> **目的**：将安全底线从 AI prompt 约束升级为硬编码拦截规则。对抗测试 AI 在执行任何操作前必须通过此审批链。
> **原则**：Prompt 可以被绕过。硬编码的规则引擎不能。

---

## 拦截架构

```
对抗测试 AI 发出操作指令
        │
        ▼
   ┌──────────────┐
   │ 规则引擎      │ ← 本文档定义的规则
   │ (Rule Engine) │
   └──┬───┬───┬───┘
      │   │   │
   ┌──┘   │   └──┐
   ▼      ▼      ▼
 允许   拒绝   审批
         (记录)  (暂停，等待人工确认)
```

---

## 拦截规则清单

### R1: 数据库破坏性操作（BLOCK）

**触发模式**（大小写不敏感匹配）：

```
DROP\s+(TABLE|DATABASE|INDEX|VIEW|SCHEMA)
TRUNCATE\s+(TABLE\s+)?\w+
DELETE\s+FROM\s+\w+(?!\s+WHERE)
ALTER\s+TABLE\s+\w+\s+DROP
```

**例外**（允许通过）：
- `DELETE FROM <table> WHERE id = <test_created_id>` — 删除测试过程中自己创建的数据
- `DROP TABLE IF EXISTS test_*` — 明确标记为测试表的 DROP

**动作**：🔴 立即拒绝。记录到审计日志。通知用户。

---

### R2: 生产数据修改（BLOCK）

**触发模式**：

```
单词匹配：production, prod_db, live_db, real_users
出现的上下文：connection_string, database_name, config 的 value
```

**例外**：无。永远不允许。

**动作**：🔴 立即拒绝。禁止整个测试会话。

---

### R3: 外部真实请求（BLOCK）

**触发模式**：

```
实际发送邮件（SMTP send）、短信（SMS API call）、支付（payment/charge API）
不包括：对被测系统自身的 HTTP 请求、对 Mock 服务的请求
```

**检测方法**：
- 检查请求 URL 是否在摘要 §4.3 定义的"可安全操作资源"白名单内
- 检查 HTTP 方法 + URL 是否匹配摘要定义的目标端点
- 如果是新的外部 URL → 🟡 审批（暂停，等待人工确认）

**动作**：不在白名单的新外部目标 → 🟡 审批。已知恶意目标 → 🔴 拒绝。

---

### R4: 配置文件修改（APPROVAL）

**触发路径模式**：

```
.env
config.yaml, config.yml, config.json, config.toml
application.properties, application.yml
settings.py, settings.js
package.json, package-lock.json, yarn.lock
Cargo.toml, pyproject.toml, pom.xml
docker-compose.yml, Dockerfile
Makefile, CMakeLists.txt
```

**例外**（允许通过）：
- 摘要 §4.3 明确标记为"可修改的测试配置文件"
- 在测试临时目录（`/tmp/adv-test/`）下的配置文件

**动作**：🟡 暂停。等待人工确认。不可自动通过。

---

### R5: 系统级操作（BLOCK）

**触发模式**：

```
kill -9, pkill, taskkill /F
systemctl stop/disable, service stop
chmod 777, chown
sudo, su
mount, umount
iptables, ufw, firewall-cmd
shutdown, reboot, init 0/6
```

**动作**：🔴 立即拒绝。无例外。

---

### R6: 递归文件删除（BLOCK）

**触发模式**：

```
rm -rf /, rm -rf /*
rm -rf ~, rm -rf $HOME
rm -rf .git
del /S /Q C:\, del /S /Q %HOMEPATH%
```

**检测方法**：检查删除命令是否包含递归标志 + 根路径或家目录。

**动作**：🔴 立即拒绝。无例外。

---

### R7: 漏洞利用深度限制（BLOCK）

**触发条件**：

- 注入测试：载荷已确认可执行（如 SQL 注入返回了实际数据）→ 停止该类别所有后续测试
- 越权测试：已确认可访问其他用户数据 → 停止该类别，不尝试更深利用
- RCE：已确认代码可执行 → 立即停止所有测试，通知用户

**动作**：🔴 停止该测试类别（或全部测试）。这是"探针原则"——确认漏洞存在即可，不做深度利用。

---

## 审批链实现逻辑

```
function approveOperation(operation):
    // R1-R2, R5-R6: 立即拒绝
    if matchesHardBlock(operation): return BLOCK
    
    // R7: 漏洞利用深度限制
    if exploitConfirmed(operation): return BLOCK_CATEGORY
    
    // R4: 配置文件
    if matchesConfigPath(operation) AND notInWhitelist(operation):
        return APPROVAL_REQUIRED
    
    // R3: 外部请求
    if isExternalRequest(operation) AND notInWhitelist(operation):
        return APPROVAL_REQUIRED
    
    // 默认：记录并通过
    logOperation(operation)
    return ALLOW
```

---

## 集成到对抗测试规范

在 `adversarial-ai-spec.md` §3.2 禁止操作清单中补充引用：

> 所有操作必须通过 `tools/templates/operation-approval-chain.md` 定义的硬编码拦截规则。Prompt 级别的安全约束是辅助性的，硬编码规则引擎的拦截才是安全底线。
