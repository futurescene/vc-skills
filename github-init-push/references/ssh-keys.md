# SSH 密钥管理指南

SSH（Secure Shell）密钥是访问 GitHub 的安全认证方式，比密码更安全、更方便。

## SSH 密钥基础

### 什么是 SSH 密钥？

SSH 密钥对包含：
- **私钥**：保密，存储在本地计算机
- **公钥**：共享，添加到 GitHub 账户

使用 SSH 密钥的优势：
- 无需每次输入密码
- 更安全（公钥加密技术）
- 更便捷（一次配置，长期使用）

### SSH 密钥算法

推荐使用的算法（按优先级）：

1. **Ed25519**（推荐）
   - 现代安全算法
   - 密钥更小，性能更好
   - 需要OpenSSH 6.5+（2014年）

2. **RSA 4096**
   - 最广泛支持
   - 密钥较大
   - 兼容性最好

3. **ECDSA**
   - 较新的算法
   - 存在一些安全担忧

## 生成 SSH 密钥

### 1. 生成 Ed25519 密钥（推荐）

```bash
# 基本生成
ssh-keygen -t ed25519 -C "your_email@example.com"

# 指定文件名
ssh-keygen -t ed25519 -C "your_email@example.com" -f ~/.ssh/id_ed25519_github

# 无密码保护（不推荐，但更方便）
ssh-keygen -t ed25519 -C "your_email@example.com" -N ""
```

**提示：**
- `-C`：添加注释（通常是邮箱）
- `-f`：指定文件名
- `-N`：设置密码（`""` 表示无密码）

### 2. 生成 RSA 4096 位密钥

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

# 无密码
ssh-keygen -t rsa -b 4096 -C "your_email@example.com" -N ""
```

### 3. 生成过程交互

```bash
$ ssh-keygen -t ed25519 -C "your_email@example.com"
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/user/.ssh/id_ed25519):  # 按 Enter 使用默认位置
Enter passphrase (empty for no passphrase):  # 输入密码或按 Enter 跳过
Enter same passphrase again:  # 再次输入密码
Your identification has been saved in /home/user/.ssh/id_ed25519
Your public key has been saved in /home/user/.ssh/id_ed25519.pub
The key fingerprint is:
SHA256:abc123... your_email@example.com
The key's randomart image is:
+--[ED25519 256]--+
|       .o.       |
|        o.       |
+----[SHA256]-----+
```

## 添加 SSH 密钥到 ssh-agent

### 什么是 ssh-agent？

ssh-agent 是一个密钥管理器，可以：
- 保存解密后的密钥
- 避免每次输入密码
- 管理多个密钥

### 启动和配置 ssh-agent

#### Windows (PowerShell)

```powershell
# 检查服务状态
Get-Service ssh-agent

# 启动服务（如果未运行）
Start-Service ssh-agent

# 设置为自动启动
Set-Service -Name ssh-agent -StartupType Automatic

# 添加密钥
ssh-add $env:USERPROFILE\.ssh\id_ed25519

# 查看已加载的密钥
ssh-add -l
```

#### macOS

```bash
# 启动 ssh-agent
eval "$(ssh-agent -s)"

# 添加密钥
ssh-add ~/.ssh/id_ed25519

# 将密钥添加到 macOS Keychain（永久存储）
ssh-add --apple-use-keychain ~/.ssh/id_ed25519
```

#### Linux

```bash
# 启动 ssh-agent
eval "$(ssh-agent -s)"

# 添加密钥
ssh-add ~/.ssh/id_ed25519

# 自动启动（添加到 ~/.bashrc 或 ~/.zshrc）
echo 'eval "$(ssh-agent -s)"' >> ~/.bashrc
echo 'ssh-add ~/.ssh/id_ed25519' >> ~/.bashrc
```

### 管理多个密钥

```bash
# 查看已加载的密钥
ssh-add -l

# 删除所有密钥
ssh-add -D

# 删除特定密钥
ssh-add -d ~/.ssh/id_ed25519

# 添加特定密钥
ssh-add ~/.ssh/id_rsa
```

## 将 SSH 公钥添加到 GitHub

### 步骤 1：复制公钥

```bash
# 方法 1：使用 cat（Linux/Mac）
cat ~/.ssh/id_ed25519.pub

# 方法 2：使用 xclip（需要安装）
cat ~/.ssh/id_ed25519.pub | xclip -selection clipboard

# 方法 3：使用 pbcopy（macOS）
pbcopy < ~/.ssh/id_ed25519.pub

# 方法 4：Windows PowerShell
cat $env:USERPROFILE\.ssh\id_ed25519.pub | Set-Clipboard

# 方法 5：使用 clip（Windows CMD）
type %USERPROFILE%\.ssh\id_ed25519.pub | clip
```

### 步骤 2：在 GitHub 上添加

1. 访问 https://github.com/settings/keys
2. 点击 **"New SSH key"** 或 **"Add SSH key"**
3. **Title**：输入描述性名称（如 "My Laptop"）
4. **Key**：粘贴公钥内容（包括 `ssh-ed25519` 开头和邮箱结尾）
5. 点击 **"Add SSH key"**

### 步骤 3：验证连接

```bash
# 测试 SSH 连接
ssh -T git@github.com

# 预期输出（首次连接）：
> The authenticity of host 'github.com (140.82.112.4)' can't be established.
> ED25519 key fingerprint is SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU.
> Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
> Warning: Permanently added 'github.com' (ED25519) to the list of known hosts.

# 成功输出：
> Hi username! You've successfully authenticated, but GitHub does not provide shell access.
```

⚠️ **注意**：最后的 "does not provide shell access" 是正常的，表示认证成功。

## SSH 配置文件

### 创建 ~/.ssh/config

配置文件可以简化 SSH 连接和管理多个密钥。

```bash
# 创建配置文件
touch ~/.ssh/config

# 设置权限（重要！）
chmod 600 ~/.ssh/config
```

### 配置示例

```ssh
# GitHub 全局配置
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_github
    IdentitiesOnly yes

# GitHub 个人账户
Host github-personal
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_personal
    IdentitiesOnly yes

# GitHub 工作账户
Host github-work
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_work
    IdentitiesOnly yes

# 自定义 SSH 选项
Host *
    AddKeysToAgent yes
    IdentityFile ~/.ssh/id_ed25519
    IdentitiesOnly yes
```

### 使用多个 GitHub 账户

```ssh
# 个人 GitHub
Host github-personal
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_personal

# 工作 GitHub
Host github-work
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_work
```

使用不同账户克隆：
```bash
# 使用个人账户
git clone git@github-personal:username/repo.git

# 使用工作账户
git clone git@github-work:company/repo.git
```

## 故障排查

### 问题 1：权限错误

**错误：** `Permissions 0644 for '~/.ssh/id_rsa' are too open.`

**解决：**
```bash
# 修复私钥权限
chmod 600 ~/.ssh/id_ed25519
chmod 600 ~/.ssh/id_rsa

# 修复公钥权限
chmod 644 ~/.ssh/id_ed25519.pub

# 修复 .ssh 目录权限
chmod 700 ~/.ssh
```

### 问题 2：SSH 连接被拒绝

**错误：** `ssh: connect to host github.com port 22: Connection refused`

**解决方案：**

1. **检查网络连接**
   ```bash
   ping github.com
   ```

2. **使用 HTTPS 端口 443**（如果端口 22 被封锁）
   ```bash
   # 编辑 ~/.ssh/config
   Host github.com
       Hostname ssh.github.com
       Port 443
       User git
   ```

3. **测试连接**
   ```bash
   ssh -T -p 443 git@ssh.github.com
   ```

### 问题 3：密钥未被识别

**错误：** `git@github.com: Permission denied (publickey).`

**解决：**
```bash
# 1. 验证密钥已添加到 agent
ssh-add -l

# 2. 如果为空，添加密钥
ssh-add ~/.ssh/id_ed25519

# 3. 检查配置文件
cat ~/.ssh/config

# 4. 使用详细模式调试
ssh -vvv git@github.com
```

### 问题 4：多个密钥冲突

**症状**：Git 使用错误的密钥。

**解决：**
```bash
# 1. 在 ~/.ssh/config 中指定 IdentityFile
Host github.com
    IdentityFile ~/.ssh/correct_key

# 2. 使用 IdentitiesOnly 强制指定
Host github.com
    IdentitiesOnly yes
    IdentityFile ~/.ssh/correct_key

# 3. 使用 GIT_SSH_COMMAND 环境变量
GIT_SSH_COMMAND="ssh -i ~/.ssh/correct_key" git push
```

### 问题 5：密钥密码遗忘

**解决：**
```bash
# 无法恢复，需要生成新密钥
ssh-keygen -t ed25519 -C "your_email@example.com"

# 然后将新公钥添加到 GitHub
```

## 安全最佳实践

### 1. 密钥密码保护

```bash
# 生成带密码的密钥
ssh-keygen -t ed25519 -C "your_email@example.com"
# 输入强密码
```

### 2. 使用 ssh-agent

```bash
# 只需输入一次密码
ssh-add ~/.ssh/id_ed25519
```

### 3. 定期轮换密钥

```bash
# 每年生成新密钥
ssh-keygen -t ed25519 -C "your_email@example.com" -f ~/.ssh/id_ed25519_2025

# 更新 GitHub 上的公钥

# 删除旧密钥
rm ~/.ssh/id_ed25519_old
```

### 4. 备份密钥

```bash
# 安全地备份私钥
cp ~/.ssh/id_ed25519 ~/backup/ssh-keys/
chmod 600 ~/backup/ssh-keys/id_ed25519

# 加密备份
tar -czf - ~/backup/ssh-keys/ | gpg -e -r your_email@example.com > ssh-keys-backup.tar.gz.gpg
```

### 5. 限制密钥使用

在 GitHub 上：
- 为每个设备使用不同的密钥
- 定期审查活动密钥
- 删除不再使用的密钥

### 6. 硬件安全密钥（可选）

使用 YubiKey 等硬件令牌：
- 私钥存储在硬件中，无法导出
- 需要物理接触才能使用
- 更高的安全性

## 高级配置

### 1. SSH 密钥过期提醒

创建脚本 `~/check-ssh-keys.sh`：
```bash
#!/bin/bash
# 检查密钥年龄，超过 1 年提醒

for key in ~/.ssh/id_*; do
    if [ -f "$key" ]; then
        age=$(( ($(date +%s) - $(stat -f %m "$key")) / 86400 ))
        if [ $age -gt 365 ]; then
            echo "Warning: $key is $age days old. Consider rotating."
        fi
    fi
done
```

### 2. 自动选择密钥

```bash
# 在 ~/.ssh/config 中
Host *
    IdentityFirst yes
    IdentitiesOnly yes
```

### 3. 连接复用

加速 SSH 连接：
```ssh
Host *
    ControlMaster auto
    ControlPath ~/.ssh/cm-%r@%h:%p
    ControlPersist 600
```

## 常用命令参考

```bash
# 生成密钥
ssh-keygen -t ed25519 -C "email@example.com"

# 查看公钥
cat ~/.ssh/id_ed25519.pub

# 查看私钥指纹
ssh-keygen -lf ~/.ssh/id_ed25519

# 测试连接
ssh -T git@github.com

# 调试模式
ssh -vvv git@github.com

# 查看已加载密钥
ssh-add -l

# 添加密钥
ssh-add ~/.ssh/id_ed25519

# 删除所有密钥
ssh-add -D

# 查看配置
cat ~/.ssh/config

# 测试配置
ssh -F ~/.ssh/config github.com
```

## 参考资源

- GitHub 官方文档：https://docs.github.com/en/authentication/connecting-to-github-with-ssh
- OpenSSH 手册：https://man.openbsd.org/ssh
- SSH 配置最佳实践：https://wiki.archlinux.org/title/SSH_keys
