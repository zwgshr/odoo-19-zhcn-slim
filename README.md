# Odoo 19.0 中文精简镜像

自动同步 Odoo 官方仓库，并清理非中文翻译文件的 GitHub Actions 工作流。

## 📋 项目简介

本项目通过 GitHub Actions 自动化工作流，定期从 [odoo/odoo](https://github.com/odoo/odoo) 官方仓库同步 19.0 分支的最新更新，并进行以下优化：

- ✅ 保留中文（zh_CN）翻译文件
- ✅ 删除其他语言的 .po 翻译文件
- ✅ 保留所有 .pot 模板文件
- ✅ 删除上游 .github 目录
- ✅ 同步信息记录在 git commit message 中

## 🚀 快速开始

### 初始化分支

首次使用时，需要手动触发初始化工作流：

1. 进入 GitHub 仓库的 **Actions** 标签页
2. 选择 **"初始化 Odoo 19.0 镜像分支"** 工作流
3. 点击 **"Run workflow"** 按钮
4. 等待工作流执行完成

初始化完成后，将创建一个全新的 `19.0` 分支，包含最新的 Odoo 19.0 代码。

### 自动同步

初始化后，工作流会自动运行：

- **定时同步**: 每天 UTC 00:00（北京时间 08:00）自动执行
- **手动触发**: 随时在 Actions 页面手动运行 "同步 Odoo 19.0 上游更新"

## 📂 项目结构

同步后的 `19.0` 分支将包含：

```
19.0/
└── [Odoo 源代码]               # 完整的 Odoo 19.0 代码
```

### 同步信息说明

所有上游同步信息都记录在 **git commit message** 中，每次同步的 commit 包含完整的元数据，无需额外的元数据文件。

## 🔧 工作流配置

### 1. 初始化工作流 (`init.yml`)

**用途**: 首次创建 19.0 分支

**触发方式**: 手动触发

**主要步骤**:
1. 克隆 Odoo 官方仓库 19.0 分支（使用 `--filter=blob:none` 优化克隆速度）
2. 删除上游 .github 目录
3. 清理非中文翻译文件
4. 创建孤儿分支并推送

### 2. 同步工作流 (`sync.yml`)

**用途**: 定期同步上游更新

**触发方式**:
- 定时: 每天 UTC 00:00
- 手动: 随时可手动触发

**智能同步**:
- 从 git commit message 中读取上次同步的 commit SHA
- 对比上游是否有新提交
- 仅在有更新时执行同步
- 无更新时自动跳过

**主要步骤**:
1. 从上次 commit message 中提取同步记录
2. 克隆上游最新代码
3. 检测是否有更新
4. 清理翻译文件
5. 提交并推送更改（同步信息记录在 commit message 中）

## 📊 监控与通知

每次同步完成后，工作流会输出：

- ✅ 同步状态（成功/跳过）
- 📌 上游 commit SHA
- 📅 上游提交日期
- 👤 上游提交作者
- 📝 提交标题
- 🔗 变更对比链接

可在 Actions 标签页查看详细日志。

## 🛠️ 高级配置

### 修改同步频率

编辑 `.github/workflows/sync.yml` 中的 cron 表达式：

```yaml
on:
  schedule:
    # 每天 UTC 00:00（北京时间 08:00）
    - cron: '0 0 * * *'
```

常用 cron 示例：
- `0 */6 * * *` - 每 6 小时
- `0 0 * * 1` - 每周一
- `0 0 1 * *` - 每月 1 号

### 同步其他 Odoo 版本

如需同步其他版本（如 18.0、17.0），可以：

1. 复制并修改工作流文件
2. 将所有 `19.0` 替换为目标版本号
3. 更新分支名称和元数据文件名

### 保留更多语言

修改清理步骤，保留多个语言：

```bash
# 保留中文和英文
find . -type f -name "*.po" ! -name "zh_CN.po" ! -name "en_US.po" -delete
```

## 📝 提交信息格式

所有同步提交都遵循统一格式：

```
同步上游 Odoo 19.0 更新

同步来自 odoo/odoo 仓库 19.0 分支的最新更新。

Upstream-Repository: https://github.com/odoo/odoo
Upstream-Branch: 19.0
Upstream-Commit: abc123...
Upstream-Date: 2025-10-27T04:34:55Z
Upstream-Author: Author Name
Upstream-Title: commit title
Upstream-Previous: xyz789...
Upstream-Compare: https://github.com/odoo/odoo/compare/xyz789...abc123
```

这样便于追踪每次同步的来源和变更。

## 🤝 贡献指南

欢迎提交 Issue 和 Pull Request！

- 提出改进建议
- 报告 Bug
- 优化工作流配置
- 完善文档

## 📄 许可证

本项目的工作流配置文件采用 MIT 许可证。

Odoo 源代码遵循其官方许可证（LGPL-3.0）。

## 🔗 相关链接

- [Odoo 官方仓库](https://github.com/odoo/odoo)
- [Odoo 官方文档](https://www.odoo.com/documentation/19.0/)
- [GitHub Actions 文档](https://docs.github.com/actions)

## ⚠️ 注意事项

1. **存储空间**: Odoo 完整代码库较大（~2-3GB），请确保仓库有足够空间
2. **网络流量**: 定时同步会消耗 GitHub Actions 配额（免费账户每月 2000 分钟）
3. **分支保护**: 建议不要在 19.0 分支上直接修改，避免同步冲突
4. **Fork 使用**: 如果是 Fork 的仓库，需要在 Settings → Actions 中启用工作流

## 🆘 故障排查

### 问题: 工作流未自动运行

**解决**:
- 检查 Actions 是否已启用（Settings → Actions → General）
- 确认 cron 语法正确
- 查看 Actions 标签页是否有错误日志

### 问题: 推送失败（权限错误）

**解决**:
- 确认仓库的 Actions 权限设置为 "Read and write permissions"
- 路径: Settings → Actions → General → Workflow permissions

### 问题: 克隆速度慢

**解决**:
- 已使用 `--filter=blob:none` 优化
- 已使用 `--depth=1` 限制历史深度
- 可考虑使用 GitHub Actions 缓存（但效果有限）

---

**维护者**: [@zwgshr](https://github.com/zwgshr)

**最后更新**: 2025-10-27


