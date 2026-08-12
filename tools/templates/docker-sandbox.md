# Docker 沙箱隔离 — 模板与指南

> 将对抗测试 Agent 在隔离的 Docker 容器中运行，防止测试操作影响宿主机。

---

## 基础 Dockerfile

```dockerfile
# adversarial-testing-sandbox
FROM ubuntu:22.04

# 最小化安装
RUN apt-get update && apt-get install -y --no-install-recommends \
    curl wget git python3 python3-pip nodejs npm \
    && rm -rf /var/lib/apt/lists/*

# 创建非 root 用户
RUN useradd -m -s /bin/bash tester
USER tester
WORKDIR /home/tester

# 挂载 skill 规范文件（只读）
# docker run -v /path/to/skill/tools:/home/tester/tools:ro ...

# 挂载输出目录（读写）
# docker run -v /path/to/output:/home/tester/output ...

COPY entrypoint.sh /home/tester/entrypoint.sh
ENTRYPOINT ["/home/tester/entrypoint.sh"]
```

---

## entrypoint.sh 模板

```bash
#!/bin/bash
set -e

echo "=== Adversarial Testing Sandbox ==="
echo "Environment: CONTAINERIZED"
echo "User: $(whoami)"
echo "Working directory: $(pwd)"
echo ""

# 验证挂载
if [ ! -f /home/tester/tools/adversarial-ai-spec.md ]; then
    echo "ERROR: tools/ not mounted. Use: -v /path/to/tools:/home/tester/tools:ro"
    exit 1
fi

# 执行测试（由调用方通过 CMD 传入）
exec "$@"
```

---

## 运行方式

### 基本用法

```bash
# 构建镜像
docker build -t adversarial-sandbox .

# 运行（只读挂载 skill 规范，读写挂载输出目录）
docker run --rm \
    --network=host \
    -v "$(pwd)/tools:/home/tester/tools:ro" \
    -v "$(pwd)/output:/home/tester/output" \
    adversarial-sandbox \
    python3 run_tests.py --summary output/summary.md
```

### Docker Compose

```yaml
version: '3.8'
services:
  adversarial-tester:
    build: .
    network_mode: host
    volumes:
      - ./tools:/home/tester/tools:ro
      - ./output:/home/tester/output
      - /var/run/docker.sock:/var/run/docker.sock  # 如需访问被测容器
    environment:
      - TEST_TARGET=http://target-service:8080
      - SAFE_MODE=strict
    security_opt:
      - no-new-privileges:true
    read_only: true
    tmpfs:
      - /tmp
    cap_drop:
      - ALL
```

---

## 安全加固建议

### 必须配置

```bash
--read-only                    # 根文件系统只读
--cap-drop=ALL                 # 移除所有 capabilities
--security-opt=no-new-privileges  # 禁止提权
--memory=512m                  # 内存限制
--cpus=1                       # CPU 限制
--network=none                 # 如不需要网络，完全断网
```

### 网络隔离

```bash
# 仅允许访问被测服务
--network=bridge
--add-host=target:192.168.1.100

# 完全隔离（离线测试）
--network=none
```

### 资源限制

```bash
--memory=512m --memory-swap=512m
--cpus=1
--pids-limit=100
--ulimit nofile=1024:2048
```

---

## 与操作审批链的关系

| 层级 | 机制 | 拦截时机 |
|------|------|---------|
| **第 1 层：Docker 沙箱** | OS 级隔离（capability drop、只读 FS、网络限制） | 执行前 — 物理不可行 |
| **第 2 层：操作审批链** | 规则引擎（R1-R7 硬编码拦截） | 执行前 — 逻辑拒绝 |
| **第 3 层：AI Prompt 约束** | 自然语言指令 | 执行前 — 建议性 |

**三层层层递进**。第 1 层是终极保险——即使审批链和 prompt 都失效，容器隔离也能防止操作系统级破坏。

---

## CI/CD 集成

在 GitHub Actions 中启动容器化测试：

```yaml
- name: Run adversarial tests in sandbox
  run: |
    docker build -t adversarial-sandbox .github/adversarial-testing/
    docker run --rm \
      --network=host \
      -v ${{ github.workspace }}/.adversarial-testing/tools:/home/tester/tools:ro \
      -v ${{ github.workspace }}/test-output:/home/tester/output \
      adversarial-sandbox
```
