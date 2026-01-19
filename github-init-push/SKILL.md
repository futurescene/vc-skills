---
name: github-init-push
description: "GitHub 仓库初始化与推送自动化：包括 SSH 密钥生成、本地 Git 初始化、.gitignore 配置、分支设置（main + dev）与代码推送。用户手动在 GitHub 网站创建仓库，本技能负责本地配置和推送。"
---

# GitHub 仓库初始化与推送技能

将本地项目推送到 GitHub 个人仓库的完整流程，自动完成 SSH 密钥配置、Git 初始化、分支设置等步骤。用户需要在 GitHub 网站手动创建仓库。

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
- 自动创建 GitHub 仓库（用户需手动在网站创建）
- 推送现有 Git 仓库（仅处理未初始化的项目）
- 处理 GitHub API 的高级功能（如 Webhook、Actions 等）
- 使用 gh CLI 或其他 GitHub API 工具

必需输入：
- 项目名称（如 `aaa-bbb`）
- GitHub 用户名（如 `yourGitHubName`）
- GitHub 邮箱（如 `yourGitHubMail@xx.com`）
- 项目路径（如 `/home/user/projects/aaa-bbb`）

## Quick Reference

### 基本使用流程

**步骤 1：检查 SSH 密钥是否存在**
```bash
# Windows (PowerShell)
Test-Path $env:USERPROFILE\.ssh\id_ed25519.pub

# Linux/Mac/Git Bash
[ -f ~/.ssh/id_ed25519.pub ] && echo "Exists" || echo "Not found"
```

**步骤 2：生成 SSH 密钥（如不存在）**
```bash
# Windows (PowerShell)
ssh-keygen -t ed25519 -C "your_email@example.com" -f $env:USERPROFILE\.ssh\id_ed25519 -N ""

# Linux/Mac/Git Bash
ssh-keygen -t ed25519 -C "your_email@example.com" -f ~/.ssh/id_ed25519 -N ""
```

**步骤 3：显示公钥内容**
```bash
# Windows (PowerShell)
cat $env:USERPROFILE\.ssh\id_ed25519.pub

# Linux/Mac/Git Bash
cat ~/.ssh/id_ed25519.pub
```

**步骤 4：测试 SSH 连接**
```bash
ssh -T git@github.com
# 预期输出：Hi username! You've successfully authenticated...
```

**步骤 5：初始化 Git 仓库**
```bash
cd /path/to/your/project
git init
git config user.name "your-username"
git config user.email "your-email@example.com"
```

**步骤 6：创建 .gitignore 文件**
```bash
# 根据项目类型选择合适的模板（见下方完整模板）
```

**步骤 7：创建并推送 main 分支**
```bash
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin git@github.com:username/repo-name.git
git push -u origin main
```

**步骤 8：创建并推送 dev 分支**
```bash
git checkout -b dev
git push -u origin dev
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

**切换分支**
```bash
git checkout main    # 切换到 main 分支
git checkout dev     # 切换到 dev 分支
```

## 完整执行流程

### 阶段 1：SSH 密钥配置

1. **检查 SSH 密钥是否存在**
   ```bash
   # Windows (PowerShell)
   Test-Path $env:USERPROFILE\.ssh\id_ed25519.pub

   # Linux/Mac/Git Bash
   ls ~/.ssh/id_ed25519.pub 2>/dev/null && echo "Exists" || echo "Not found"
   ```

2. **生成 SSH 密钥（如不存在）**
   ```bash
   # 使用 ed25519 算法（推荐）
   ssh-keygen -t ed25519 -C "your_email@example.com" -f ~/.ssh/id_ed25519 -N ""

   # 或使用 RSA 4096 位
   ssh-keygen -t rsa -b 4096 -C "your_email@example.com" -f ~/.ssh/id_rsa -N ""
   ```

3. **启动 SSH 代理并添加密钥**
   ```bash
   # Windows (PowerShell)
   Start-Service ssh-agent
   ssh-add $env:USERPROFILE\.ssh\id_ed25519

   # Linux/Mac/Git Bash
   eval "$(ssh-agent -s)"
   ssh-add ~/.ssh/id_ed25519
   ```

4. **显示公钥内容**
   ```bash
   # Windows
   cat $env:USERPROFILE\.ssh\id_ed25519.pub

   # Linux/Mac
   cat ~/.ssh/id_ed25519.pub
   ```

5. **🛑 询问检查点 1：SSH 公钥配置**

   ⚠️ **询问用户：**
   ```
   你的 SSH 公钥已生成（如上所示）。

   请确认是否已在 GitHub 添加此公钥：

   操作步骤：
   1. 访问：https://github.com/settings/keys
   2. 点击 "New SSH key"
   3. Title 输入：项目名称（如 "aaa-bbb"）
   4. Key 粘贴上面显示的公钥（整行内容）
   5. 点击 "Add SSH key"

   请回复：
   - "已添加"：如果你已完成配置
   - "未完成"：如果需要更多时间
   ```

6. **测试 SSH 连接**
   ```bash
   ssh -T git@github.com
   # 预期输出：Hi username! You've successfully authenticated, but GitHub does not provide shell access.
   ```

### 阶段 2：本地 Git 初始化

1. **进入项目目录**
   ```bash
   cd /path/to/your/project
   ```

2. **初始化 Git 仓库**
   ```bash
   git init
   ```

3. **配置 Git 用户信息**
   ```bash
   git config user.name "your-username"
   git config user.email "your-email@example.com"
   ```

4. **创建 .gitignore 文件**（根据项目类型选择下方模板）
   ```bash
   # 创建适合项目类型的 .gitignore 文件
   ```

5. **首次提交**
   ```bash
   git add .
   git commit -m "Initial commit: Project setup"
   ```

6. **重命名为 main 分支**
   ```bash
   git branch -M main
   ```

### 阶段 3：GitHub 仓库创建（用户手动操作）

1. **🛑 询问检查点 2：创建 GitHub 仓库**

   ⚠️ **询问用户：**
   ```
   现在需要在 GitHub 上创建仓库。

   请按以下步骤操作：

   1. 访问：https://github.com/new
   2. Repository name 输入：[项目名称]
   3. 选择 Public 或 Private
   4. ❌ 不要勾选 "Add a README file"（我们已经有了）
   5. ❌ 不要勾选 "Add .gitignore"（我们已经创建）
   6. ❌ 不要选择 "Choose a license"（可后续添加）
   7. 点击 "Create repository"

   创建完成后，请回复 "已创建"。
   ```

2. **添加远程仓库**
   ```bash
   git remote add origin git@github.com:username/repo-name.git
   ```

3. **验证远程仓库**
   ```bash
   git remote -v
   # 应显示：
   # origin  git@github.com:username/repo-name.git (fetch)
   # origin  git@github.com:username/repo-name.git (push)
   ```

### 阶段 4：分支设置与推送

1. **推送 main 分支**
   ```bash
   git push -u origin main
   ```

2. **创建 dev 分支**
   ```bash
   git checkout -b dev
   ```

3. **推送 dev 分支**
   ```bash
   git push -u origin dev
   ```

4. **切换回 main 分支**
   ```bash
   git checkout main
   ```

### 阶段 5：验证

1. **验证远程分支**
   ```bash
   git branch -r
   # 应显示：
   # origin/main
   # origin/dev
   ```

2. **验证本地分支**
   ```bash
   git branch
   # 应显示：
   # * main
   #   dev
   ```

3. **验证分支追踪关系**
   ```bash
   git branch -vv
   # 应显示 main 和 dev 的追踪关系
   ```

4. **验证 GitHub 仓库**
   - 访问：https://github.com/username/repo-name
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
2. **询问检查点 1**：等待用户在 GitHub 添加公钥
3. 测试 SSH 连接
4. 创建 Python 专用 .gitignore
5. 初始化 Git 并提交
6. **询问检查点 2**：等待用户在 GitHub 创建仓库
7. 推送 main 和 dev 分支

**预期输出：**
- 本地有 main 和 dev 分支
- GitHub 有 `yourGitHubName/aaa-bbb` 仓库
- 仓库包含 main 和 dev 分支
- .gitignore 正确配置
- 所有代码已推送

### Example 2：Node.js 项目推送到 GitHub

**输入：**
- 项目名称：`my-web-app`
- GitHub 用户名：`yourGitHubName`
- 邮箱：`yourGitHubMail@xx.com`
- 项目类型：Node.js 项目

**步骤：**

1. 检查 SSH 密钥（已存在，跳过生成）
2. **询问检查点 1**：确认公钥已配置
3. 创建 Node.js 专用 .gitignore
4. 初始化 Git 并提交
5. **询问检查点 2**：等待用户创建仓库
6. 推送分支

**预期输出：**
- Git 仓库已初始化
- node_modules/ 已被忽略
- main 和 dev 分支已推送
- 可通过 `git status` 确认干净的工作目录

### Example 3：Java 项目推送到 GitHub

**输入：**
- 项目名称：`spring-boot-app`
- GitHub 用户名：`yourGitHubName`
- 邮箱：`yourGitHubMail@xx.com`
- 项目类型：Java/Maven 项目

**步骤：**

1. 生成 SSH 密钥
2. **询问检查点 1**：等待用户配置公钥
3. 创建 Java 专用 .gitignore
4. 初始化 Git 并提交
5. **询问检查点 2**：等待用户创建仓库
6. 推送分支

**预期输出：**
- target/ 目录已被忽略
- Maven 配置文件正确处理
- main 和 dev 分支已推送

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
# 方案 1：强制推送（谨慎使用，仅首次推送时）
git push -f origin main

# 方案 2：先拉取再推送（如仓库已有内容）
git pull origin main --allow-unrelated-histories
git push origin main
```

### 问题 3：仓库不存在

**错误：** `ERROR: Repository not found.`

**解决方案：**
- 确认已在 GitHub 网站创建仓库
- 确认仓库名称拼写正确
- 确认你有该仓库的推送权限

### 问题 4：.gitignore 不生效

**原因：** 文件已被 Git 跟踪

**解决方案：**
```bash
# 清除缓存
git rm -r --cached .

# 重新添加
git add .

# 提交
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
   - 建议通过 PR 合并 dev → main

3. **首次提交**
   - 确保 .gitignore 正确配置
   - 检查没有敏感信息被提交
   - 提交信息清晰描述

4. **仓库可见性**
   - 公开仓库：任何人可见
   - 私有仓库：仅你和授权用户可见
   - 创建后可在 GitHub 设置中修改

5. **询问检查点**
   - 本技能包含两个询问检查点
   - 确保用户完成手动操作后再继续
   - 提供清晰的操作指导

## References

- `references/github-cli.md`: GitHub CLI 完整文档（可选参考）
- `references/git-workflow.md`: Git 工作流最佳实践
- `references/ssh-keys.md`: SSH 密钥管理指南

## Maintenance

- Sources: GitHub 官方文档, Git 官方文档
- Last updated: 2025-01-19
- Known limits: 需要用户手动在 GitHub 网站创建仓库和配置 SSH 密钥
