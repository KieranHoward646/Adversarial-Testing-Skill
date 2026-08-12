# 技术栈 → 攻击载荷映射

> 开发者 AI 和对抗测试 AI 可根据被测产品的技术栈，优先选择相关载荷。

---

## 语言/运行时

| 技术栈 | 优先载荷 | 说明 |
|--------|---------|------|
| **Node.js / Express** | `injection-payloads.md` (NoSQL, SSTI), `jwt-payloads.md`, `deserialization-payloads.md` (node-serialize) | NoSQL 注入常见；JWT 广泛使用；node-serialize 反序列化风险 |
| **Python / Flask / Django** | `injection-payloads.md` (SSTI Jinja2, SQL, 命令注入), `deserialization-payloads.md` (Pickle), `ssrf-payloads.md` | Jinja2 SSTI 高发；Pickle 反序列化是已知风险；Requests 库 SSRF |
| **Python / FastAPI** | `injection-payloads.md` (SQL, 命令注入), `jwt-payloads.md`, `ssrf-payloads.md` | JWT 认证常见；异步特性可能引入竞态 |
| **Java / Spring Boot** | `injection-payloads.md` (SQL, 命令注入), `deserialization-payloads.md` (Java), `jwt-payloads.md`, `file-upload-payloads.md` | Java 反序列化历史漏洞多；Spring Security JWT 配置复杂 |
| **PHP** | `injection-payloads.md` (SQL, XSS, 命令注入), `deserialization-payloads.md` (PHP), `file-upload-payloads.md` | 文件上传 + PHP 执行路径是经典攻击面 |
| **Ruby / Rails** | `injection-payloads.md` (SQL, SSTI ERB), `deserialization-payloads.md` | ERB SSTI；YAML 反序列化历史漏洞 |
| **Go** | `injection-payloads.md` (SQL, 命令注入), `jwt-payloads.md`, `ssrf-payloads.md` | Go 反序列化风险较低；HTTP 客户端 SSRF 需注意 |
| **Rust** | `injection-payloads.md` (SQL, 命令注入), `ssrf-payloads.md` | 内存安全但逻辑漏洞仍需测试 |
| **C# / .NET** | `injection-payloads.md` (SQL, 命令注入), `deserialization-payloads.md` (.NET), `file-upload-payloads.md` | BinaryFormatter 反序列化高危；ViewState 风险 |

---

## 框架

| 框架 | 优先载荷 | 说明 |
|------|---------|------|
| **Express** | NoSQL 注入、JWT 篡改、SSRF | 中间件链可能导致安全问题 |
| **Django** | SQL 注入（ORM 绕过）、SSTI、CSRF | ORM 可能被错误拼接绕过 |
| **Spring Boot** | Java 反序列化、JWT、SpEL 注入 | Actuator 端点暴露；SpEL 注入风险 |
| **Laravel** | PHP 反序列化、文件上传、SQL | 队列系统中的反序列化风险 |
| **Next.js** | SSRF、JWT、API Route 注入 | SSR 中的 SSRF；API Routes 直接暴露 |
| **React** | XSS（dangerouslySetInnerHTML） | 客户端框架，但服务端渲染增大了攻击面 |

---

## 数据库

| 数据库 | 优先载荷 | 说明 |
|--------|---------|------|
| **MySQL / MariaDB** | SQL 注入（UNION、时间盲注、堆叠查询） | 支持多语句；information_schema 可遍历 |
| **PostgreSQL** | SQL 注入（pg_sleep、COPY、大对象） | pg_sleep 可用于盲注；COPY 可读写文件 |
| **MongoDB** | NoSQL 注入（$gt, $ne, $where） | 无 Schema 约束，注入更灵活 |
| **Redis** | SSRF（dict://, gopher:// 协议） | 未授权访问 + RCE 经典组合 |
| **SQLite** | SQL 注入（无网络隔离） | 文件级数据库，注入可读写任意文件 |

---

## 外部集成

| 集成类型 | 优先载荷 | 说明 |
|---------|---------|------|
| **AWS 环境** | SSRF（169.254.169.254 元数据）、JWT（Cognito）、文件上传（S3） | 元数据服务是 SSRF 首选目标 |
| **GCP 环境** | SSRF（metadata.google.internal） | GCP 元数据端点 |
| **Azure 环境** | SSRF（169.254.169.254）、JWT（AD） | Azure IMDS 端点 |
| **阿里云 / 腾讯云** | SSRF（100.100.100.200 / metadata.tencentyun.com） | 国内云元数据端点 |
| **OAuth 2.0 / OIDC** | JWT 篡改、重定向劫持、state 参数爆破 | OAuth 流程复杂，实现易出错 |
| **Webhook 回调** | SSRF、命令注入、任意 URL 跳转 | Webhook URL 是 SSRF 入口 |
| **文件存储 (S3/OSS)** | 文件上传、SSRF、公开访问检测 | 错误配置可能导致数据泄露 |

---

## LLM / AI 系统

| 类型 | 优先载荷 | 说明 |
|------|---------|------|
| **Chat 应用 (GPT 类)** | `llm-payloads.md` (Prompt Injection, Jailbreak) | 核心攻击面是自然语言 |
| **RAG 系统** | `llm-payloads.md` (RAG Poisoning) + SSRF | 检索源可被投毒；文档抓取可能触发 SSRF |
| **Agent 系统** | `llm-payloads.md` (Tool-Protocol) + 命令注入 + SSRF | 工具调用链扩大攻击面 |
| **多模态系统** | `llm-payloads.md` (Visual/Audio attacks) + 文件上传 | 图片/音频可嵌入注入指令 |

---

## 使用方式

### 开发者 AI

产出摘要时，参考此映射：
1. 确定被测产品的技术栈
2. 从映射表中选择优先载荷类别
3. 在摘要 §3 中标注这些类别的测试点

### 对抗测试 AI

执行测试时，参考此映射：
1. 读取摘要中的技术栈信息
2. 从此映射表获取推荐载荷
3. 先执行高匹配度载荷，再扩展其他类别
