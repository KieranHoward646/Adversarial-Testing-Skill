# Patch 代码生成规范

> 对抗测试 AI 在修复问题时，不仅记录修改描述，还应生成可直接 apply 的 diff 格式 Patch。

---

## Patch 输出要求

每个修复必须提供以下信息：

### 1. 元信息

```markdown
- **文件**：src/auth/login.js
- **行号**：45-52
- **修复类型**：添加输入校验
- **关联发现**：B-001
- **严重度**：🟠 High (7/10)
```

### 2. diff 格式 Patch

````markdown
```diff
--- a/src/auth/login.js
+++ b/src/auth/login.js
@@ -45,7 +45,12 @@
 function handleLogin(req, res) {
   const { email, password } = req.body;
-  // 直接使用用户输入
-  const user = await db.query(`SELECT * FROM users WHERE email = '${email}'`);
+  // 校验 email 格式
+  if (!email || !/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email)) {
+    return res.status(400).json({ error: 'INVALID_EMAIL' });
+  }
+  // 参数化查询
+  const user = await db.query('SELECT * FROM users WHERE email = ?', [email]);
   if (!user) {
     return res.status(401).json({ error: 'INVALID_CREDENTIALS' });
   }
```
````

### 3. 修复后验证

```markdown
- **修复后测试**：重新执行 I-001（SQL 注入测试）→ PASS
- **回归测试**：合法邮箱登录 → PASS；非法邮箱 → 正确返回 400
```

---

## diff 格式规范

### Unified Diff 格式

```
--- a/path/to/file
+++ b/path/to/file
@@ -起始行,行数 +起始行,行数 @@
 上下文行（不变）
-删除的行
+增加的行
 上下文行（不变）
```

### 规则

1. 至少包含 3 行上下文（`diff -U3`）
2. 每个 hunk（`@@` 区块）处理一个逻辑变更
3. 不相关的修改拆分为独立的 diff 块
4. 注释说明修改意图
5. 保持代码风格一致

---

## 修复类型模板

### 类型 1：添加输入校验

```diff
@@ -45,7 +45,12 @@
 function handler(req, res) {
   const input = req.body.field;
+  if (!input || typeof input !== 'string' || input.length > 100) {
+    return res.status(400).json({ error: 'INVALID_INPUT' });
+  }
   process(input);
```

### 类型 2：修复 SQL 注入（参数化查询）

```diff
-  const result = await db.query(`SELECT * FROM t WHERE col = '${input}'`);
+  const result = await db.query('SELECT * FROM t WHERE col = ?', [input]);
```

### 类型 3：添加权限校验

```diff
   const resource = await getResource(req.params.id);
+  if (resource.ownerId !== req.user.id) {
+    return res.status(403).json({ error: 'FORBIDDEN' });
+  }
   return res.json(resource);
```

### 类型 4：添加速率限制

```diff
   app.post('/api/login', 
+    rateLimit({ windowMs: 15 * 60 * 1000, max: 5 }),
     loginHandler
   );
```

### 类型 5：添加异常处理

```diff
-  const data = JSON.parse(rawInput);
+  try {
+    const data = JSON.parse(rawInput);
+  } catch (e) {
+    return res.status(400).json({ error: 'INVALID_JSON' });
+  }
```

---

## 无法生成 Patch 的情况

以下情况不生成 Patch，而是在报告中给出详细的文字修复建议：

- 架构级修改（如"需要从 REST 迁移到 GraphQL"）
- 外部依赖更新（如"升级到 Spring Boot 3.x"）
- 需要人工判断的修改（如"重新设计权限模型"）
- 跨多个文件的连锁修改（提供每个文件的独立建议）
