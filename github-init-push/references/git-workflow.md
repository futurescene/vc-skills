# Git 工作流最佳实践

本文档介绍 Git 的最佳实践和工作流程。

## 基础概念

### Git 的三个区域
```
工作目录 (Working Directory)
    ↓ git add
暂存区 (Staging Area / Index)
    ↓ git commit
本地仓库 (Local Repository)
    ↓ git push
远程仓库 (Remote Repository)
```

### 分支模型
```
main (or master) - 生产环境代码
  ↑ 合并
dev - 开发环境代码
  ↑ 合并
feature/* - 功能分支
bugfix/* - 修复分支
hotfix/* - 紧急修复
```

## 基本工作流

### 1. Feature Branch 工作流

```bash
# 1. 从 dev 创建功能分支
git checkout dev
git pull origin dev
git checkout -b feature/awesome-feature

# 2. 开发并提交
git add .
git commit -m "Add awesome feature"

# 3. 推送到远程
git push -u origin feature/awesome-feature

# 4. 创建 PR 合并到 dev
# (通过 GitHub UI 或 gh CLI)

# 5. 完成后删除分支
git branch -d feature/awesome-feature
```

### 2. Git Flow 工作流

```bash
# 初始化 Git Flow
git flow init

# 开始新功能
git flow feature start my-feature

# 完成功能（合并到 dev）
git flow feature finish my-feature

# 开始发布
git flow release start v1.0.0

# 完成发布（合并到 main 和 dev，打标签）
git flow release finish v1.0.0

# 开始紧急修复
git flow hotfix fix-critical-bug

# 完成修复
git flow hotfix finish fix-critical-bug
```

### 3. GitHub Flow 工作流

```bash
# 1. 从 main 创建分支
git checkout main
git pull origin main
git checkout -b my-feature

# 2. 提交更改
git add .
git commit -m "Add feature"

# 3. 推送并创建 PR
git push -u origin my-feature
gh pr create --title "My feature" --body "描述"

# 4. 代码审查后合并到 main

# 5. 部署并删除分支
```

## 提交规范

### Conventional Commits

```bash
# 格式
<type>(<scope>): <subject>

<body>

<footer>
```

### 类型 (Type)

- `feat`: 新功能
- `fix`: 修复 bug
- `docs`: 文档更新
- `style`: 代码格式（不影响代码运行）
- `refactor`: 重构
- `perf`: 性能优化
- `test`: 测试相关
- `chore`: 构建/工具链相关
- `ci`: CI/CD 相关

### 示例

```bash
# 简单提交
git commit -m "feat: add user authentication"

# 带范围的提交
git commit -m "feat(auth): add OAuth2 login support"

# 带详细描述
git commit -m "fix(api): handle null response

Add null check for API response to prevent crash.
This fixes issue #123."

# 破坏性更改
git commit -m "feat!: change API response format

BREAKING CHANGE: API response now uses camelCase instead of snake_case."
```

## 分支管理

### 分支命名规范

```bash
# 功能分支
feature/feature-name
feature/123-feature-name

# 修复分支
bugfix/bug-description
bugfix/456-fix-login

# 发布分支
release/v1.0.0
release/1.0.0

# 热修复分支
hotfix/critical-bug
hotfix/789-security-fix
```

### 分支操作

```bash
# 列出所有分支
git branch -a

# 创建分支
git branch my-branch

# 切换分支
git checkout my-branch

# 创建并切换
git checkout -b my-branch

# 重命名分支
git branch -m old-name new-name

# 删除分支
git branch -d my-branch  # 安全删除（已合并）
git branch -D my-branch  # 强制删除

# 推送新分支
git push -u origin my-branch

# 删除远程分支
git push origin --delete my-branch

# 跟踪远程分支
git branch --set-upstream-to=origin/dev dev
```

## 合并与变基

### Merge (合并)

```bash
# 合并分支（创建合并提交）
git merge feature-branch

# --no-fast-forward：总是创建合并提交
git merge --no-ff feature-branch

# --squash：压缩所有提交为一个
git merge --squash feature-branch
```

### Rebase (变基)

```bash
# 变基到 main
git checkout feature-branch
git rebase main

# 交互式变基（修改历史）
git rebase -i HEAD~3  # 最近 3 个提交
```

### 什么时候使用 Merge vs Rebase

**使用 Merge 当：**
- 你想保留完整的历史记录
- 团队协作时避免重写历史
- 不确定是否要变基

**使用 Rebase 当：**
- 清理本地提交历史
- 保持线性历史
- 尚未推送的本地提交

⚠️ **注意：不要对已推送的提交进行变基！**

## 常用场景

### 场景 1：修复最后一条提交

```bash
# 修改文件
git add file.txt
git commit --amend --no-edit  # 不修改提交消息
# 或
git commit --amend -m "新的提交消息"
```

### 场景 2：合并多个提交

```bash
# 交互式变基
git rebase -i HEAD~3

# 在编辑器中将要合并的提交从 'pick' 改为 'squash' 或 's'
# 保存并退出，然后编辑合并后的提交消息
```

### 场景 3：暂存未完成的工作

```bash
# 暂存所有更改
git stash save "WIP: work in progress"

# 查看暂存列表
git stash list

# 应用暂存（不删除）
git stash apply

# 应用并删除暂存
git stash pop

# 删除特定暂存
git stash drop stash@{0}

# 清除所有暂存
git stash clear
```

### 场景 4：撤销更改

```bash
# 撤销工作目录的更改
git restore file.txt
git restore .

# 撤销暂存的更改
git restore --staged file.txt

# 撤销提交（保留更改）
git reset --soft HEAD~1

# 撤销提交（不保留更改）
git reset --hard HEAD~1

# 撤销已推送的提交（创建新提交）
git revert abc123
```

### 场景 5： cherry-pick (挑选提交)

```bash
# 挑选特定提交应用到当前分支
git cherry-pick abc123

# 挑选多个提交
git cherry-pick abc123..def456

# 只应用更改但不提交
git cherry-pick -n abc123
```

## 远程协作

### 远程仓库操作

```bash
# 查看远程仓库
git remote -v

# 添加远程仓库
git remote add origin https://github.com/owner/repo.git

# 更改远程仓库 URL
git remote set-url origin git@github.com:owner/repo.git

# 删除远程仓库
git remote remove origin

# 获取远程更改（不合并）
git fetch origin

# 获取并合并
git pull origin main

# 拉取并变基
git pull --rebase origin main
```

### Fork 协作

```bash
# 1. Fork 原仓库（在 GitHub 上）

# 2. 克隆你的 fork
git clone git@github.com:your-username/repo.git
cd repo

# 3. 添加原仓库为上游
git remote add upstream git@github.com:original-owner/repo.git

# 4. 创建功能分支
git checkout -b feature-branch

# 5. 开发并提交
git add .
git commit -m "Add feature"

# 6. 推送到你的 fork
git push -u origin feature-branch

# 7. 创建 PR（在 GitHub 上）

# 8. 定期同步上游更新
git fetch upstream
git checkout main
git merge upstream/main
git push origin main
```

## .gitignore 最佳实践

### 模板结构

```gitignore
# 操作系统
.DS_Store
Thumbs.db

# IDE
.vscode/
.idea/

# 依赖
node_modules/
vendor/

# 构建产物
dist/
build/

# 环境变量
.env
.env.local

# 日志
*.log

# 测试覆盖率
coverage/

# 临时文件
*.tmp
*.cache
```

### 排除已跟踪文件

```bash
# 从 Git 跟踪中移除但保留文件
git rm --cached file.txt

# 从 Git 跟踪中移除并删除文件
git rm file.txt

# 停止跟踪目录
git rm -r --cached directory/
```

## 安全与隐私

### 敏感信息处理

```bash
# 从历史中完全删除敏感文件
git filter-branch --force --index-filter \
  "git rm --cached --ignore-unmatch secrets.txt" \
  --prune-empty --tag-name-filter cat -- --all

# 更推荐使用 BFG Repo-Cleaner
# https://rtyley.github.io/bfg-repo-cleaner/
```

### 提交前检查

```bash
# 搜索敏感信息
git grep -i password
git grep -i api_key
git grep -i secret
```

### .gitignore 中的敏感文件

```gitignore
# 敏感配置文件
config/secrets.yml
.env.production
*.pem
*.key
credentials.json
```

## 性能优化

### 浅克隆

```bash
# 只克隆最近的提交（节省空间和时间）
git clone --depth 1 https://github.com/owner/repo.git

# 克隆特定分支
git clone --branch dev --depth 1 https://github.com/owner/repo.git
```

### 部分克隆

```bash
# 只克隆特定目录
git clone --depth 1 --filter=blob:none --sparse https://github.com/owner/repo.git
cd repo
git sparse-checkout set path/to/directory
```

### 清理

```bash
# 清理未跟踪文件
git clean -fd

# 清理包括 ignored 文件
git clean -fdX

# 压缩仓库
git gc --prune=now --aggressive
```

## 故障排查

### 合并冲突

```bash
# 1. 查看冲突文件
git status

# 2. 手动编辑冲突文件（搜索 <<<<<<<）

# 3. 标记为已解决
git add file.txt

# 4. 完成合并
git commit

# 或中止合并
git merge --abort
```

### 分支丢失

```bash
# 查找丢失的提交
git reflog

# 恢复丢失的分支
git checkout -b recovered-branch abc123
```

### 远程分支不同步

```bash
# 查看远程分支
git branch -r

# 更新远程分支列表
git remote update origin --prune

# 删除不存在的远程分支跟踪
git fetch -p
```

## 最佳实践总结

1. **频繁提交，小步前进**
   - 小而频繁的提交更容易回滚和审查

2. **清晰的提交消息**
   - 使用约定式提交规范
   - 说明"为什么"而不是"是什么"

3. **分支保护**
   - 保护 main 分支，要求 PR 和审查
   - 使用状态检查

4. **代码审查**
   - 所有代码通过 PR 合并
   - 至少一人审查

5. **保持主分支稳定**
   - main/master 应始终可部署
   - 使用 CI/CD 自动测试

6. **文档化**
   - README.md
   - CONTRIBUTING.md
   - CHANGELOG.md

7. **定期同步**
   - 定期从上游拉取更新
   - 解决冲突及时

8. **使用标签**
   - 为发布打标签
   - 使用语义化版本

## 工具推荐

### Git 客户端
- **GitHub Desktop**: 图形化 Git 客户端
- **Sourcetree**: 免费，功能强大
- **GitKraken**: 跨平台，可视化
- **Tower**: Mac 最佳 Git 客户端

### 命令行增强
- **git-extras**: 额外的 Git 命令
- **gh-dash**: GitHub CLI 的仪表板
- **lazygit**: 终端 UI

### Git 托管平台
- **GitHub**: 最流行，功能全面
- **GitLab**: CI/CD 强大
- **Bitbucket**: Atlassian 生态

## 参考资源

- 官方文档: https://git-scm.com/doc
- Git Handbook: https://guides.github.com/introduction/git-handbook/
- Conventional Commits: https://www.conventionalcommits.org/
- Git Flow: https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow
