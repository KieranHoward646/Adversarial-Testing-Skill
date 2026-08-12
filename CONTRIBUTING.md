# Contributing to Adversarial Testing Skill

欢迎贡献！无论是新的攻击载荷、平台适配器、理论文档还是 bug 修复，都请先阅读本指南。

## 贡献方式

### 1. 提交攻击载荷

如果你有针对特定框架/技术的有效攻击载荷：

1. 在 `community/` 下创建 `payload-<framework>.md`（参考 `community/test-case-template.md`）
2. 每个载荷需包含：触发条件、预期行为、实际验证结果
3. 注明测试环境（OS、语言版本、框架版本）

### 2. 添加平台适配器

如果你的 AI 编程助手不在现有适配器列表中：

1. 在 `adapters/` 下创建 `<platform-name>/` 目录
2. 参考 `adapters/generic/` 的结构
3. 必须包含 `INSTALL.md` 安装指南

### 3. 完善理论文档

`theory/` 下的文档始终欢迎补充：

- 新的真实案例（附来源链接）
- 方法论改进建议
- 翻译（README 已有中/英/法/西，欢迎更多语言）

### 4. 报告 Bug 或提建议

在 GitHub Issues 中提交，使用以下标签：
- `bug` — 功能异常
- `enhancement` — 改进建议
- `payload` — 新攻击载荷
- `adapter` — 新平台适配器
- `docs` — 文档问题

## 提交规范

### Commit 信息格式

```
<type>: <简短描述>

<详细说明（可选）>
```

类型：
- `payload:` — 新增/更新攻击载荷
- `adapter:` — 新增/更新平台适配器
- `theory:` — 理论文档更新
- `tools:` — 工具/模板更新
- `fix:` — bug 修复
- `docs:` — README/文档更新

### Pull Request 流程

1. Fork 仓库
2. 创建 feature 分支 (`git checkout -b payload/jwt-attacks`)
3. 提交更改
4. 推送到你的 Fork
5. 提交 PR 到 `main` 分支

PR 标题格式：`[类型] 简短描述`（如 `[payload] 添加 JWT 篡改攻击载荷`）

## 行为准则

- 所有贡献的攻击载荷**仅用于授权测试**
- 不要在 PR 中包含真实密码、Token 或生产环境信息
- 载荷文件必须包含安全警告

## License

贡献的代码和内容以 MIT 许可证发布。提交 PR 即表示你同意此条款。
