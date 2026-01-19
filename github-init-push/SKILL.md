---
name: github-init-push
description: "GitHub 仓库初始化与推送自动化：包括 SSH 密钥生成、远程仓库创建、本地 Git 初始化、.gitignore 配置、分支设置（main + dev）与代码推送。适用于新建项目推送到 GitHub 仓库。"
---

# GitHub 仓库初始化与推送技能

一键将本地项目推送到 GitHub 个人仓库，自动完成 SSH 密钥配置、仓库创建、分支初始化等所有必要步骤。

## When to Use This Skill

当你需要：
- 将本地已存在的项目推送到新建的 GitHub 仓库
- 自动创建 SSH 密钥并配置 GitHub 认证
- 一键完成 Git 初始化和远程仓库设置
- 创建标准的 main + dev 分支结构
- 生成符合最佳实践的 .gitignore 文件

## Not For / Boundaries

本技能不会：
- 修改项目源代码
- 处理 GitHub API 的高级功能（如 Webhook、Actions 等）
- 推送现有 Git 仓库（仅处理未初始化的项目）
- 处理私有仓库的复杂权限配置

必需输入：
- 项目名称（如 `aaa-bbb`）
- GitHub 用户名（如 `yourGitHubName`）
- GitHub 邮箱（如 `yourGitHubMail@xx.com`）

## Quick Reference

### 基本使用流程

**步骤 1：检查 SSH 密钥是否存在**
```bash
# Windows
ls $env:USERPROFILE\.ssh\id_rsa.pub 2>$null

# Linux/Mac
ls ~/.ssh/id_rsa.pub 2>/dev/null
```

**步骤 2：生成 SSH 密钥（如不存在）**
```bash
# Windows
ssh-keygen -t ed25519 -C "your_email@example.com" -f $env:USERPROFILE\.ssh\id_ed25519 -N ""

# Linux/Mac
ssh-keygen -t ed25519 -C "your_email@example.com" -f ~/.ssh/id_ed25519 -N ""
```

**步骤 3：显示公钥内容（用于添加到 GitHub）**
```bash
# Windows
cat $env:USERPROFILE\.ssh\id_ed25519.pub

# Linux/Mac
cat ~/.ssh/id_ed25519.pub
```

**步骤 4：初始化 Git 仓库**
```bash
cd /path/to/your/project
git init
git config user.name "your-username"
git config user.email "your-email@example.com"
```

**步骤 5：创建 .gitignore 文件**
```bash
# 创建通用 .gitignore（参考下方完整模板）
```

**步骤 6：创建并推送 main 分支**
```bash
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin git@github.com:username/repo-name.git
git push -u origin main
```

**步骤 7：创建并推送 dev 分支**
```bash
git checkout -b dev
git push -u origin dev
```

**步骤 8：在 GitHub 上合并 dev 到 main（可选）**
```bash
# 通过 GitHub UI 或使用 gh CLI：
gh pr create --base main --head dev --title "Merge dev to main" --body "Initial setup"
gh pr merge
```

### 常用命令

**测试 SSH 连接**
```bash
ssh -T git@github.com
```

**查看远程仓库**
```bash
git remote -v
```

**查看所有分支**
```bash
git branch -a
```

**删除远程仓库并重新添加**
```bash
git remote remove origin
git remote add origin git@github.com:username/repo-name.git
```

## 完整执行流程

### 阶段 1：SSH 密钥配置（唯一需要人工干预的步骤）

1. **检查 SSH 密钥是否存在**
   ```bash
   # Windows
   Test-Path $env:USERPROFILE\.ssh\id_ed25519.pub

   # Linux/Mac
   [ -f ~/.ssh/id_ed25519.pub ] && echo "Exists" || echo "Not found"
   ```

2. **生成 SSH 密钥（如不存在）**
   ```bash
   # 使用 ed25519 算法（推荐）
   ssh-keygen -t ed25519 -C "your_email@example.com" -f ~/.ssh/id_ed25519 -N ""
   ```

3. **启动 SSH 代理并添加密钥**
   ```bash
   # Windows
   Start-Service ssh-agent
   ssh-add $env:USERPROFILE\.ssh\id_ed25519

   # Linux/Mac
   eval "$(ssh-agent -s)"
   ssh-add ~/.ssh/id_ed25519
   ```

4. **显示公钥并等待用户配置**
   ```bash
   cat ~/.ssh/id_ed25519.pub
   ```
   ⚠️ **在此暂停，等待用户完成以下操作：**
   - 复制显示的公钥
   - 访问 https://github.com/settings/keys
   - 点击 "New SSH key"
   - 粘贴公钥并保存
   - 确认后继续执行

5. **测试 SSH 连接**
   ```bash
   ssh -T git@github.com
   # 预期输出：Hi username! You've successfully authenticated...
   ```

### 阶段 2：GitHub 仓库创建（使用 gh CLI）

1. **检查 gh CLI 是否已安装**
   ```bash
   gh --version
   # 如未安装，提示用户访问 https://cli.github.com/
   ```

2. **登录 GitHub（如未登录）**
   ```bash
   gh auth login
   # 选择：GitHub.com -> SSH -> Yes (上传 ssh key) -> Login with a web browser
   ```

3. **创建新仓库**
   ```bash
   gh repo create repo-name --public --source=. --remote=origin --push
   ```

### 阶段 3：本地 Git 初始化

1. **初始化 Git 仓库**
   ```bash
   cd /path/to/project
   git init
   ```

2. **配置 Git 用户信息**
   ```bash
   git config user.name "your-username"
   git config user.email "your-email@example.com"
   ```

3. **创建 .gitignore 文件**（见下方模板）
   ```bash
   # 创建智能 .gitignore，根据项目类型自动调整
   ```

4. **首次提交**
   ```bash
   git add .
   git commit -m "Initial commit: Project setup"
   ```

### 阶段 4：分支设置与推送

1. **重命名为 main 分支**
   ```bash
   git branch -M main
   ```

2. **添加远程仓库**
   ```bash
   git remote add origin git@github.com:username/repo-name.git
   ```

3. **推送 main 分支**
   ```bash
   git push -u origin main
   ```

4. **创建 dev 分支**
   ```bash
   git checkout -b dev
   ```

5. **推送 dev 分支**
   ```bash
   git push -u origin dev
   ```

6. **（可选）在 GitHub 上设置默认分支**
   ```bash
   gh repo edit --default-branch main
   ```

### 阶段 5：验证

1. **验证远程分支**
   ```bash
   git branch -r
   # 应显示：origin/main 和 origin/dev
   ```

2. **验证本地分支追踪**
   ```bash
   git branch -vv
   # 应显示 main 和 dev 的追踪关系
   ```

3. **验证 GitHub 仓库**
   - 访问 https://github.com/username/repo-name
   - 确认 main 和 dev 分支都存在
   - 确认代码已正确推送

## .gitignore 模板

### 通用 .gitignore（适用于大多数项目）

```gitignore
# 操作系统文件
.DS_Store
.DS_Store?
._*
.Spotlight-V100
.Trashes
ehthumbs.db
Thumbs.db
desktop.ini

# IDE 和编辑器
.vscode/
.idea/
*.swp
*.swo
*~
.project
.classpath
.settings/
*.sublime-project
*.sublime-workspace

# 依赖和构建产物
node_modules/
bower_components/
vendor/
dist/
build/
*.egg-info/
.venv/
venv/
ENV/
env/

# 日志和临时文件
*.log
npm-debug.log*
yarn-debug.log*
yarn-error.log*
*.tmp
*.temp
*.cache

# 环境配置
.env
.env.local
.env.*.local
*.key
*.pem
secrets/

# 测试覆盖率
coverage/
.nyc_output/
.coverage
*.cover

# 数据库
*.sqlite
*.db

# 备份文件
*.bak
*.backup
*~
```

### Python 项目专用 .gitignore

```gitignore
# Byte-compiled / optimized / DLL files
__pycache__/
*.py[cod]
*$py.class

# C extensions
*.so

# Distribution / packaging
.Python
build/
develop-eggs/
dist/
downloads/
eggs/
.eggs/
lib/
lib64/
parts/
sdist/
var/
wheels/
*.egg-info/
.installed.cfg
*.egg

# PyInstaller
*.manifest
*.spec

# Unit test / coverage reports
htmlcov/
.tox/
.coverage
.coverage.*
.cache
nosetests.xml
coverage.xml
*.cover
.hypothesis/
.pytest_cache/

# Virtual environments
.venv
venv/
ENV/
env/

# Jupyter Notebook
.ipynb_checkpoints

# pyenv
.python-version

# Environment variables
.env
.venv
```

### Node.js 项目专用 .gitignore

```gitignore
# Logs
logs
*.log
npm-debug.log*
yarn-debug.log*
yarn-error.log*
lerna-debug.log*
pnpm-debug.log*

# Dependencies
node_modules/
dist/
dist-ssr/
*.local

# Editor directories and files
.vscode/*
!.vscode/extensions.json
.idea
.DS_Store
*.suo
*.ntvs*
*.njsproj
*.sln
*.sw?

# Testing
coverage/
.nyc_output/

# Production
/build
```

### Java 项目专用 .gitignore

```gitignore
# Compiled class file
*.class

# Log file
*.log

# BlueJ files
*.ctxt

# Mobile Tools for Java (J2ME)
.mtj.tmp/

# Package Files
*.jar
*.war
*.nar
*.ear
*.zip
*.tar.gz
*.rar

# Virtual machine crash logs
hs_err_pid*
replay_pid*

# Maven
target/
pom.xml.tag
pom.xml.releaseBackup
pom.xml.versionsBackup
pom.xml.next
release.properties
dependency-reduced-pom.xml
buildNumber.properties
.mvn/timing.properties
.mvn/wrapper/maven-wrapper.jar

# Gradle
.gradle
build/
!gradle/wrapper/gradle-wrapper.jar
!**/src/main/**/build/
!**/src/test/**/build/

# IDE
.idea/
*.iws
*.iml
*.ipr
out/
.idea_modules/
*.iml
*.ipr
*.iws
.idea/
.classpath
.project
.settings/
bin/
tmp/
*.tmp
*.bak
*.swp
*~.nib
local.properties
.loadpath

# Eclipse
.metadata
.recommenders
bin/
tmp/
*.tmp
*.bak
*.swp
*~.nib
.settings/
.loadpath
.recommenders

# NetBeans
/nbproject/private/
/nbbuild/
/dist/
/nbdist/
/.nb-gradle/

# VS Code
.vscode/

# Spring Boot
spring-boot-*.log

# Application specific
application-local.properties
application-dev.properties
application-local.yml
application-dev.yml

# Mac
.DS_Store

# Windows
Thumbs.db
ehthumbs.db
Desktop.ini

# Linux
*~
.directory

# Test coverage
.jacoco/
jacoco.exec

# Lombok
lombok.config
```

## Examples

### Example 1：全新 Python 项目推送到 GitHub

**输入：**
- 项目名称：`aaa-bbb`
- GitHub 用户名：`yourGitHubName`
- 邮箱：`yourGitHubMail@xx.com`
- 项目类型：Python 项目
- 项目路径：`/home/user/projects/aaa-bbb`

**步骤：**

1. 检查并生成 SSH 密钥
2. 等待用户在 GitHub 上添加公钥
3. 创建 Python 专用 .gitignore
4. 初始化 Git 并提交
5. 使用 gh CLI 创建 GitHub 仓库
6. 推送 main 和 dev 分支

**预期输出：**
- 本地有 main 和 dev 分支
- GitHub 有 `yourGitHubName/aaa-bbb` 公开仓库
- 仓库包含 main 和 dev 分支
- .gitignore 正确配置
- 所有代码已推送

### Example 2：Node.js 项目推送到 GitHub

**输入：**
- 项目名称：`my-web-app`
- GitHub 用户名：`johndoe`
- 邮箱：`john@example.com`
- 项目类型：Node.js 项目

**步骤：**

1. 检查 SSH 密钥（已存在，跳过生成）
2. 创建 Node.js 专用 .gitignore
3. 初始化 Git 并提交
4. 创建 GitHub 仓库
5. 推送分支

**预期输出：**
- Git 仓库已初始化
- node_modules/ 已被忽略
- main 和 dev 分支已推送
- 可通过 `git status` 确认干净的工作目录

### Example 3：多语言混合项目

**输入：**
- 项目名称：`fullstack-app`
- GitHub 用户名：`developer`
- 邮箱：`dev@example.com`
- 项目类型：前端 (Node.js) + 后端 (Python)

**步骤：**

1. 生成 SSH 密钥
2. 等待用户配置 GitHub
3. 创建组合型 .gitignore（Python + Node.js）
4. 分别忽略 `frontend/node_modules/` 和 `backend/.venv/`
5. 初始化并推送

**预期输出：**
- 单一仓库包含前后端代码
- 两个目录的依赖都被正确忽略
- 分支结构清晰

## 常见问题排查

### 问题 1：SSH 连接失败

**错误：** `ssh: connect to host github.com port 22: Connection refused`

**解决方案：**
```bash
# 检查 SSH 密钥是否存在
ls ~/.ssh/id_ed25519*

# 检查 SSH 代理是否运行
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519

# 测试连接
ssh -T git@github.com
```

### 问题 2：推送被拒绝

**错误：** `! [rejected] main -> main (fetch first)`

**解决方案：**
```bash
# 方案 1：强制推送（谨慎使用）
git push -f origin main

# 方案 2：先拉取再推送
git pull origin main --allow-unrelated-histories
git push origin main
```

### 问题 3：gh CLI 未登录

**错误：** `gh not logged in`

**解决方案：**
```bash
gh auth login
# 按提示选择 GitHub.com -> SSH -> 浏览器登录
```

### 问题 4：.gitignore 不生效

**原因：** 文件已被 Git 跟踪

**解决方案：**
```bash
# 清除缓存
git rm -r --cached .

# 重新添加
git add .
git commit -m "Update .gitignore"
```

## 注意事项

1. **SSH 密钥安全**
   - 私钥永远不要分享
   - 公钥才能添加到 GitHub
   - 使用密码保护密钥（可选）

2. **分支策略**
   - main：生产环境代码
   - dev：开发环境代码
   - 建议使用 PR 合并 dev → main

3. **首次提交**
   - 确保 .gitignore 正确配置
   - 检查没有敏感信息被提交
   - 提交信息清晰描述

4. **仓库可见性**
   - 公开仓库：任何人可见
   - 私有仓库：仅你和授权用户可见
   - 创建后可修改设置

## References

- `references/github-cli.md`: GitHub CLI 完整文档
- `references/git-workflow.md`: Git 工作流最佳实践
- `references/ssh-keys.md`: SSH 密钥管理指南

## Maintenance

- Sources: GitHub 官方文档, Git 官方文档
- Last updated: 2025-01-19
- Known limits: 需要安装 gh CLI 和 Git
