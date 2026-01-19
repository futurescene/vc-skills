# GitHub CLI (gh) 参考文档

GitHub CLI 是 GitHub 的官方命令行工具，让你可以直接从终端操作 GitHub。

## 安装

### Windows
```bash
winget install --id GitHub.cli
# 或使用 Scoop
scoop install gh
```

### macOS
```bash
brew install gh
```

### Linux
```bash
# Debian/Ubuntu
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null
sudo apt update
sudo apt install gh
```

## 认证

### 使用浏览器登录（推荐）
```bash
gh auth login
# 选择选项：
# 1. GitHub.com
# 2. SSH
# 3. Yes (上传 SSH 密钥)
# 4. Login with a web browser
```

### 使用 Personal Access Token
```bash
gh auth login --with-token
# 然后粘贴你的 token
```

### 检查认证状态
```bash
gh auth status
```

### 登出
```bash
gh auth logout
```

## 仓库管理

### 创建新仓库
```bash
# 基本用法
gh repo create repo-name

# 创建公开仓库
gh repo create repo-name --public

# 创建私有仓库
gh repo create repo-name --private

# 从当前目录初始化并推送
gh repo create repo-name --source=. --remote=origin --push

# 添加描述和主页
gh repo create repo-name --description "项目描述" --homepage "https://example.com"

# 禁用问题讨论
gh repo create repo-name --disable-issues
```

### 查看仓库信息
```bash
gh repo view
gh repo view owner/repo-name
```

### 编辑仓库设置
```bash
# 修改描述
gh repo edit --description "新描述"

# 修改默认分支
gh repo edit --default-branch dev

# 改为私有仓库
gh repo edit --visibility private
```

### 删除仓库
```bash
gh repo delete owner/repo-name
```

### 列出仓库
```bash
# 列出你的所有仓库
gh repo list

# 限制数量
gh repo list --limit 10

# 按名称过滤
gh repo list --query "my-project"
```

## 问题 (Issues)

### 创建问题
```bash
gh issue create --title "问题标题" --body "问题描述"

# 从文件读取内容
gh issue create --title "Bug" --body @issue-template.md

# 指定标签和里程碑
gh issue create --title "Bug" --labels "bug,high-priority" --milestone "v1.0"
```

### 查看问题
```bash
# 列出所有问题
gh issue list

# 查看特定问题
gh issue view 123

# 查看所有开放的问题
gh issue list --state open
```

### 关闭问题
```bash
gh issue close 123

# 添加关闭评论
gh issue close 123 --comment "已修复"
```

## Pull Requests

### 创建 PR
```bash
# 基本 PR
gh pr create --title "PR 标题" --body "PR 描述"

# 指定基础分支
gh pr create --base main --head dev

# 从模板创建
gh pr create --title "Feature X" --body @pr-template.md

# 草稿 PR
gh pr create --draft
```

### 查看 PR
```bash
# 列出所有 PR
gh pr list

# 查看特定 PR
gh pr view 456

# 查看 PR 的 diff
gh pr diff 456
```

### 合并 PR
```bash
# 合并 PR
gh pr merge 456

# 使用特定合并方法
gh pr merge 456 --merge
gh pr merge 456 --squash
gh pr merge 456 --rebase

# 删除分支
gh pr merge 456 --delete-branch
```

### 检出 PR
```bash
gh pr checkout 456
```

## GitHub Actions

### 列出工作流运行
```bash
gh run list
```

### 查看运行详情
```bash
gh run view 456

# 查看日志
gh run view 456 --log
```

### 重新运行工作流
```bash
gh run rerun 456
```

## Release

### 创建 Release
```bash
gh release create v1.0.0 --notes "发布说明"

# 从文件读取发布说明
gh release create v1.0.0 --notes @release-notes.md

# 上传资产
gh release create v1.0.0 ./artifacts/app.zip
```

### 列出 Release
```bash
gh release list
```

### 查看 Release
```bash
gh release view v1.0.0
```

## Git 与 GitHub 集成

### 克隆仓库
```bash
# 克隆到当前目录
gh repo clone owner/repo-name

# 克隆到指定目录
gh repo clone owner/repo-name my-dir
```

### Fork 仓库
```bash
gh repo fork owner/repo-name

# 克隆 fork
gh repo fork owner/repo-name --clone
```

## 常用命令组合

### 完整工作流示例
```bash
# 1. 登录
gh auth login

# 2. 创建仓库并推送
gh repo create my-project --public --source=. --push

# 3. 创建 Issue
gh issue create --title "Add feature X" --body "我们需要添加功能 X"

# 4. 创建并合并 PR
git checkout -b feature-x
# ... 进行开发 ...
git push origin feature-x
gh pr create --title "Feature X" --body "实现功能 X"
gh pr merge
```

## 配置

### 查看配置
```bash
gh config
```

### 设置 Git 凭证助手
```bash
gh auth setup-git
```

### 设置编辑器
```bash
gh config set editor vim
```

## 扩展功能

### 安装扩展
```bash
gh extension install owner/extension-name
```

### 列出扩展
```bash
gh extension list
```

### 常用扩展
```bash
# gh-cos: 在 PR 中添加 Emoji 评论
gh extension install github/gh-cos

# gh-ee: 增强 Issue 和 PR 编辑器
gh extension BuyYourself/gh-ee
```

## 提示和技巧

### 自动补全
```bash
# Bash
echo 'eval "$(gh completion -s bash)"' >> ~/.bashrc

# Zsh
echo 'eval "$(gh completion -s zsh)"' >> ~/.zshrc

# Fish
gh completion -s fish | source
```

### 使用别名
```bash
# 在 ~/.bashrc 或 ~/.zshrc 中添加
alias prs='gh pr list'
alias issues='gh issue list'
alias repo='gh repo view'
```

### 格式化输出
```bash
# JSON 格式
gh repo view --json name,visibility

# 表格格式
gh issue list --json title,state --jq '.[] | [.title, .state] | @tsv'
```

## 故障排查

### 认证失败
```bash
# 重新认证
gh auth logout
gh auth login

# 检查 token
gh auth token
```

### API 限流
```bash
# 查看限流状态
gh api /rate_limit
```

### 调试模式
```bash
gh api --help
GH_DEBUG=1 gh repo list
```

## 最佳实践

1. **定期更新**: `gh upgrade`（需要从源码安装）
2. **使用脚本**: 结合 shell 脚本自动化工作流
3. **别名**: 为常用命令创建别名
4. **模板**: 为 Issues 和 PR 创建可重用模板
5. **安全**: 不要在脚本中硬编码 token，使用 `gh auth token`

## 参考资源

- 官方文档: https://docs.github.com/en/cli
- 手册页: `gh --help` 或 `man gh`
- Release notes: https://github.com/cli/cli/releases
