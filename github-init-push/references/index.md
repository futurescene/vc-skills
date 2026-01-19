# GitHub 初始化与推送技能 - 参考文档索引

本目录包含 GitHub 仓库初始化与推送相关的详细参考文档。

## 文档列表

### 1. [GitHub CLI (gh) 参考文档](./github-cli.md)
完整的 GitHub CLI 命令参考，包括：
- gh CLI 安装与配置
- 仓库管理（创建、查看、删除）
- Issue 和 Pull Request 管理
- GitHub Actions 集成
- Release 管理
- 常用命令组合和技巧

**适用场景：**
- 使用命令行工具操作 GitHub
- 自动化 GitHub 工作流
- 替代网页界面操作

### 2. [Git 工作流最佳实践](./git-workflow.md)
Git 版本控制的完整指南，包括：
- 基本概念和三个区域
- Feature Branch、Git Flow、GitHub Flow 工作流
- Conventional Commits 提交规范
- 分支管理策略
- 合并与变基选择
- 常用场景和解决方案
- 远程协作和 Fork 工作流
- .gitignore 最佳实践
- 安全与隐私保护

**适用场景：**
- 学习 Git 最佳实践
- 建立团队工作流规范
- 解决常见 Git 问题

### 3. [SSH 密钥管理指南](./ssh-keys.md)
SSH 密钥的完整管理指南，包括：
- SSH 密钥基础和算法选择
- 生成 Ed25519 和 RSA 密钥
- ssh-agent 配置和使用
- 添加公钥到 GitHub
- SSH 配置文件设置
- 多账户管理
- 故障排查
- 安全最佳实践
- 高级配置和优化

**适用场景：**
- 首次配置 SSH 访问 GitHub
- 管理多个 GitHub 账户
- 解决 SSH 连接问题
- 提高 SSH 使用安全性

## 快速导航

### 按任务查找

**我想...**
- [创建 GitHub 仓库](./github-cli.md#仓库管理)
- [配置 SSH 访问](./ssh-keys.md#生成-ssh-密钥)
- [学习 Git 工作流](./git-workflow.md#基本工作流)
- [解决 SSH 连接问题](./ssh-keys.md#故障排查)
- [设置 Git 提交规范](./git-workflow.md#提交规范)
- [管理多个 GitHub 账户](./ssh-keys.md#使用多个-github-账户)
- [创建 Pull Request](./github-cli.md#pull-requests)

### 按技能级别查找

**初学者**
1. [SSH 密钥管理指南 - 生成 SSH 密钥](./ssh-keys.md#生成-ssh-密钥)
2. [GitHub CLI - 创建仓库](./github-cli.md#仓库管理)
3. [Git 工作流 - 基本概念](./git-workflow.md#基础概念)

**中级用户**
1. [Git 工作流 - 分支管理](./git-workflow.md#分支管理)
2. [SSH 配置 - 多账户管理](./ssh-keys.md#使用多个-github-账户)
3. [GitHub CLI - Pull Request](./github-cli.md#pull-requests)

**高级用户**
1. [Git 工作流 - 合并与变基](./git-workflow.md#合并与变基)
2. [SSH - 高级配置](./ssh-keys.md#高级配置)
3. [Git - 常用场景](./git-workflow.md#常用场景)

## 核心概念速查

### Git 三个区域
```
工作目录 → [git add] → 暂存区 → [git commit] → 本地仓库 → [git push] → 远程仓库
```

### 分支策略
- `main`：生产环境代码
- `dev`：开发环境代码
- `feature/*`：功能开发分支
- `bugfix/*`：Bug 修复分支

### SSH 密钥类型
- **Ed25519**：推荐，现代安全算法
- **RSA 4096**：兼容性最好

### 提交规范
- `feat`：新功能
- `fix`：修复 bug
- `docs`：文档更新
- `refactor`：重构
- `test`：测试相关

## 常用命令速查表

### Git 基础
```bash
git init                    # 初始化仓库
git clone <url>             # 克隆仓库
git add .                   # 添加所有更改
git commit -m "message"     # 提交更改
git push                    # 推送到远程
git pull                    # 拉取远程更改
```

### GitHub CLI
```bash
gh auth login               # 登录
gh repo create <name>       # 创建仓库
gh pr create                # 创建 PR
gh issue create             # 创建 Issue
```

### SSH
```bash
ssh-keygen -t ed25519       # 生成密钥
ssh-add ~/.ssh/id_ed25519   # 添加密钥到 agent
ssh -T git@github.com       # 测试连接
```

## 学习路径建议

### 路径 1：新手入门（30 分钟）
1. 阅读 [SSH 密钥管理指南 - 生成 SSH 密钥](./ssh-keys.md#生成-ssh-密钥)
2. 完成 [GitHub CLI - 安装与认证](./github-cli.md#安装)
3. 跟随 [Git 工作流 - 基本工作流](./git-workflow.md#基本工作流) 实践

### 路径 2：进阶提升（1 小时）
1. 学习 [Git 工作流 - 分支管理](./git-workflow.md#分支管理)
2. 掌握 [Git 工作流 - 提交规范](./git-workflow.md#提交规范)
3. 了解 [SSH - 配置文件](./ssh-keys.md#ssh-配置文件)

### 路径 3：高级精通（2 小时）
1. 深入 [Git 工作流 - 合并与变基](./git-workflow.md#合并与变基)
2. 学习 [SSH - 多账户管理](./ssh-keys.md#使用多个-github-账户)
3. 实践 [Git 工作流 - 常用场景](./git-workflow.md#常用场景)

## 故障排查索引

### SSH 相关问题
- [权限错误](./ssh-keys.md#问题-1权限错误)
- [连接被拒绝](./ssh-keys.md#问题-2ssh-连接被拒绝)
- [密钥未被识别](./ssh-keys.md#问题-3密钥未被识别)
- [多个密钥冲突](./ssh-keys.md#问题-4多个密钥冲突)

### Git 相关问题
- [合并冲突](./git-workflow.md#合并冲突)
- [分支丢失](./git-workflow.md#分支丢失)
- [远程分支不同步](./git-workflow.md#远程分支不同步)
- [撤销更改](./git-workflow.md#场景-4撤销更改)

### GitHub CLI 相关问题
- [认证失败](./github-cli.md#故障排查)
- [API 限流](./github-cli.md#api-限流)

## 最佳实践摘要

### 安全
1. ✅ 为 SSH 密钥设置密码
2. ✅ 使用 ssh-agent 管理密钥
3. ✅ 定期轮换密钥（建议每年）
4. ✅ 使用 Ed25519 算法
5. ✅ 不同设备使用不同密钥

### Git 工作流
1. ✅ 频繁提交，小步前进
2. ✅ 使用约定式提交规范
3. ✅ 保护 main 分支
4. ✅ 所有代码通过 PR 合并
5. ✅ 保持主分支稳定

### 团队协作
1. ✅ 使用统一的分支策略
2. ✅ 代码审查必须
3. ✅ 编写清晰的 PR 描述
4. ✅ 定期同步上游更新
5. ✅ 及时解决冲突

## 外部资源

### 官方文档
- [GitHub 官方文档](https://docs.github.com)
- [Git 官方文档](https://git-scm.com/doc)
- [GitHub CLI 官方文档](https://cli.github.com/manual/)

### 工具
- [GitHub Desktop](https://desktop.github.com/)
- [Sourcetree](https://www.sourcetreeapp.com/)
- [GitKraken](https://www.gitkraken.com/)

### 学习资源
- [GitHub Skills](https://skills.github.com/)
- [Git Handbook](https://guides.github.com/introduction/git-handbook/)
- [Conventional Commits](https://www.conventionalcommits.org/)

## 更新日志

- **2025-01-19**: 初始版本，创建三个核心参考文档
  - GitHub CLI 参考文档
  - Git 工作流最佳实践
  - SSH 密钥管理指南

## 贡献

如果你发现文档有错误或需要补充，欢迎：
1. 提交 Issue 报告问题
2. 提交 PR 改进文档
3. 分享你的使用经验

## 许可证

本文档遵循项目主许可证：MIT License
