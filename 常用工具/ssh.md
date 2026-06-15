# SSH 密钥生成与绑定

SSH 密钥是一种比密码更安全的身份验证方式，广泛应用于 GitHub/GitLab 代码托管、服务器登录、rsync 备份等场景。本文将手把手教你生成、管理、绑定 SSH 密钥。

---

## 一、什么是 SSH 密钥？

SSH 密钥采用**非对称加密**技术，包含两个部分：

| 组成部分 | 文件名（默认）                   | 作用               | 保密性                 |
| :------- | :------------------------------- | :----------------- | :--------------------- |
| **私钥** | `id_rsa` 或 `id_ed25519`         | 解密数据、证明身份 | **绝密，绝不能泄露**   |
| **公钥** | `id_rsa.pub` 或 `id_ed25519.pub` | 加密数据、验证身份 | 可以公开，放在服务器上 |

**工作原理**：你把公钥交给服务器（如 GitHub），服务器用公钥加密一个挑战字符串发送给你。你的电脑用私钥解密后返回，服务器验证成功即允许登录。这个过程全程不传输密码，非常安全。

---

## 二、检查是否已有 SSH 密钥

在生成新密钥前，先检查系统是否已存在密钥：

```bash
ls -la ~/.ssh/
```

常见输出示例：
```
-rw-------  1 user  staff  1679  5月 31 10:00 id_ed25519
-rw-r--r--  1 user  staff   399  5月 31 10:00 id_ed25519.pub
-rw-------  1 user  staff  3289  5月 31 10:00 id_rsa
-rw-r--r--  1 user  staff   749  5月 31 10:00 id_rsa.pub
-rw-------  1 user  staff   195  5月 31 10:00 known_hosts
```

**文件说明**：
- `id_*`：私钥文件（无 `.pub` 后缀）
- `id_*.pub`：公钥文件
- `known_hosts`：记录你信任的服务器
- `config`：SSH 配置文件（可选，用于管理多个密钥）

如果你看到 `id_ed25519.pub` 或 `id_rsa.pub`，说明已有密钥。可以跳过生成步骤，直接进入「查看公钥」部分。

---

## 三、生成新的 SSH 密钥

### 3.1 选择加密算法

目前主要有两种算法可选：

| 算法        | 命令             | 密钥长度 | 安全性 | 兼容性              | 推荐度   |
| :---------- | :--------------- | :------- | :----- | :------------------ | :------- |
| **ED25519** | `-t ed25519`     | 256 位   | ⭐⭐⭐⭐⭐  | 较新系统/服务都支持 | **首选** |
| **RSA**     | `-t rsa -b 4096` | 4096 位  | ⭐⭐⭐⭐   | 几乎所有服务都支持  | 备选     |

> **建议**：默认使用 ED25519，它更安全、更快、密钥更短。如果连接的老旧服务器不支持，再用 RSA。

### 3.2 生成 ED25519 密钥（推荐）

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

参数说明：
- `-t ed25519`：指定使用 ED25519 算法
- `-C`：添加注释（通常用邮箱标识，方便区分）

### 3.3 生成 RSA 密钥（备选）

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

参数说明：
- `-b 4096`：指定密钥长度为 4096 位（默认 3072，建议 4096）

### 3.4 交互过程详解

执行命令后，会出现如下交互提示：

```bash
Generating public/private ed25519 key pair.
Enter file in which to save the key (/Users/你的用户名/.ssh/id_ed25519):
```

**第一步：选择保存路径**
- 直接按回车使用默认路径（推荐）
- 或输入自定义路径，如 `/Users/你的用户名/.ssh/github_ed25519`

```bash
Enter passphrase (empty for no passphrase):
```

**第二步：设置密码短语（强烈建议设置）**
- 密码短语是对私钥的二次加密保护
- 即使私钥文件泄露，没有密码短语也无法使用
- 输入后需要再确认一次

> **什么是密码短语？** 类似于给私钥加一个密码。每次使用 SSH 连接时需要输入。如果你希望完全免密，可以留空，但不推荐。

```bash
Enter same passphrase again:
Your identification has been saved in /Users/.../.ssh/id_ed25519
Your public key has been saved in /Users/.../.ssh/id_ed25519.pub
The key fingerprint is:
SHA256:xxxxxxxxxxxxxxxxxxxxxxxxxxxxx your_email@example.com
The key's randomart image is:
+--[ED25519 256]--+
|    .o=*BO+      |
|     o=+=B .     |
|      . O.       |
|       . .       |
|        S .      |
|         .       |
|                 |
|                 |
|                 |
+----[SHA256]-----+
```

看到以上输出，说明密钥生成成功。

### 3.5 一键生成命令（带所有参数）

如果你不想交互，可以直接指定所有参数：

```bash
ssh-keygen -t ed25519 -C "your_email@example.com" -f ~/.ssh/id_ed25519 -N "你的密码短语"
```

参数：
- `-f`：指定私钥文件路径
- `-N`：指定密码短语

---

## 四、查看和管理公钥

### 4.1 查看公钥内容

```bash
cat ~/.ssh/id_ed25519.pub
```

输出示例：
```
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIxxxxxxx... your_email@example.com
```

### 4.2 复制公钥到剪贴板

**macOS**：
```bash
pbcopy < ~/.ssh/id_ed25519.pub
# 或
cat ~/.ssh/id_ed25519.pub | pbcopy
```

**Linux**：
```bash
xclip -sel clip < ~/.ssh/id_ed25519.pub
# 如果没有 xclip，先安装：sudo apt install xclip
```

**Windows (Git Bash/WSL)**：
```bash
clip < ~/.ssh/id_ed25519.pub
```

### 4.3 查看私钥指纹

```bash
ssh-keygen -lf ~/.ssh/id_ed25519.pub
```

输出示例：
```
256 SHA256:xxxxxxxxxxxxxxxxxxxxxxxxxxxxx your_email@example.com (ED25519)
```

---

## 五、绑定到 GitHub

### 5.1 方法一：命令行（使用 gh 工具）

如果你安装了 GitHub CLI (`gh`)：

```bash
gh auth login
gh ssh-key add ~/.ssh/id_ed25519.pub --title "我的MacBook Pro"
```

### 5.2 方法二：网页手动添加（最常用）

**步骤 1**：复制公钥到剪贴板
```bash
pbcopy < ~/.ssh/id_ed25519.pub
```

**步骤 2**：打开 GitHub 设置页面
- 登录 GitHub
- 点击右上角头像 → **Settings**
- 左侧菜单点击 **SSH and GPG keys**

**步骤 3**：添加新密钥
- 点击 **New SSH key** 按钮
- **Title**：输入一个标识（如 `MacBook Pro M1`、`办公电脑`）
- **Key type**：选择 `Authentication Key`
- **Key**：粘贴公钥内容（Ctrl+V 或 Cmd+V）
- 点击 **Add SSH key**

**步骤 4**：验证连接
```bash
ssh -T git@github.com
```

成功输出：
```
Hi 你的用户名! You've successfully authenticated, but GitHub does not provide shell access.
```

---

## 六、绑定到 GitLab

### 6.1 复制公钥

```bash
cat ~/.ssh/id_ed25519.pub
```

### 6.2 网页添加

1. 登录 GitLab
2. 点击右上角头像 → **Preferences**
3. 左侧菜单点击 **SSH Keys**
4. 粘贴公钥到 **Key** 文本框
5. **Title** 可选，建议填写设备标识
6. **Expiration date** 可选（留空表示永不过期）
7. 点击 **Add key**

### 6.3 验证连接

```bash
ssh -T git@gitlab.com
```

成功输出：
```
Welcome to GitLab, @你的用户名!
```

---

## 七、绑定到服务器（Linux/VPS）

### 7.1 方法一：使用 ssh-copy-id（推荐）

```bash
ssh-copy-id -i ~/.ssh/id_ed25519.pub user@服务器IP
```

示例：
```bash
ssh-copy-id -i ~/.ssh/id_ed25519.pub root@192.168.1.100
```

系统会提示输入服务器密码，验证成功后公钥自动添加到服务器的 `~/.ssh/authorized_keys`。

### 7.2 方法二：手动添加

**步骤 1**：复制本地公钥
```bash
cat ~/.ssh/id_ed25519.pub
```

**步骤 2**：SSH 登录服务器
```bash
ssh user@服务器IP
```

**步骤 3**：在服务器上添加公钥
```bash
mkdir -p ~/.ssh
echo "粘贴你的公钥内容" >> ~/.ssh/authorized_keys
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

**步骤 4**：退出并测试
```bash
exit
ssh user@服务器IP   # 应该不再需要密码
```

---

## 八、管理多个 SSH 密钥（配置文件）

当你有多个密钥（GitHub 一个、GitLab 一个、公司服务器一个）时，需要配置 `~/.ssh/config`。

### 8.1 创建配置文件

```bash
touch ~/.ssh/config
chmod 600 ~/.ssh/config
```

### 8.2 配置文件模板

```bash
# GitHub 个人账号
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_personal
    IdentitiesOnly yes

# GitHub 工作账号
Host github-work
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_work
    IdentitiesOnly yes

# GitLab
Host gitlab.com
    HostName gitlab.com
    User git
    IdentityFile ~/.ssh/id_ed25519_gitlab
    IdentitiesOnly yes

# 个人服务器
Host myserver
    HostName 192.168.1.100
    User root
    Port 22
    IdentityFile ~/.ssh/id_ed25519_server
    IdentitiesOnly yes
```

### 8.3 配置参数说明

| 参数             | 作用                                          |
| :--------------- | :-------------------------------------------- |
| `Host`           | 别名，SSH 命令时使用                          |
| `HostName`       | 真实服务器地址                                |
| `User`           | 登录用户名                                    |
| `Port`           | SSH 端口（默认 22）                           |
| `IdentityFile`   | 使用的私钥路径                                |
| `IdentitiesOnly` | 只使用指定的密钥，忽略 ssh-agent 中的其他密钥 |

### 8.4 使用配置

配置完成后，可以简写 SSH 命令：

```bash
# 原本需要
ssh -i ~/.ssh/id_ed25519_server root@192.168.1.100

# 配置后只需
ssh myserver

# GitHub 克隆仓库
git clone git@github-work:公司/项目.git
```

---

## 九、SSH Agent 管理

SSH Agent 是一个后台程序，用于管理私钥。使用它可以避免重复输入密码短语。

### 9.1 启动 SSH Agent

```bash
eval "$(ssh-agent -s)"
```

输出示例：
```
Agent pid 12345
```

### 9.2 添加私钥到 Agent

```bash
ssh-add ~/.ssh/id_ed25519
```

如果需要输入密码短语，在此输入一次即可，之后会话中无需再次输入。

### 9.3 查看已加载的密钥

```bash
ssh-add -l
```

### 9.4 删除密钥

```bash
ssh-add -d ~/.ssh/id_ed25519   # 删除指定密钥
ssh-add -D                      # 清空所有密钥
```

### 9.5 macOS 特殊配置（将密钥永久存入钥匙串）

macOS 可以设置密码短语永久保存在钥匙串中：

```bash
ssh-add --apple-use-keychain ~/.ssh/id_ed25519
```

并在 `~/.ssh/config` 中添加：
```bash
Host *
    UseKeychain yes
    AddKeysToAgent yes
```

---

## 十、常见问题与解决方案

### 10.1 Permission denied (publickey)

**错误信息**：
```
git@github.com: Permission denied (publickey).
```

**解决方法**：
1. 确认公钥已添加到 GitHub 账户
2. 检查本地 SSH Agent 是否有私钥：`ssh-add -l`
3. 添加私钥：`ssh-add ~/.ssh/id_ed25519`
4. 测试连接：`ssh -T git@github.com`

### 10.2 确认使用正确的密钥

```bash
ssh -vT git@github.com
```

`-v` 参数会显示详细日志，包括使用了哪个密钥文件。

### 10.3 多个 GitHub 账号冲突

如果多个账号使用相同的 Host，需要配置文件区分：

```bash
# ~/.ssh/config
Host github-personal
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_personal

Host github-work
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_work
```

克隆仓库时：
```bash
git clone git@github-personal:用户名/项目.git
```

### 10.4 权限错误

**错误信息**：
```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
Permissions 0644 for '/Users/.../.ssh/id_ed25519' are too open.
```

**解决方法**：修复私钥权限
```bash
chmod 600 ~/.ssh/id_ed25519
chmod 644 ~/.ssh/id_ed25519.pub
chmod 700 ~/.ssh
```

### 10.5 密码短语忘记

如果忘记了私钥的密码短语，无法恢复，只能**重新生成**新的密钥对，然后替换所有服务器上的公钥。

### 10.6 更换电脑后需要迁移

1. 备份旧电脑的 `~/.ssh` 目录
2. 复制到新电脑的 `~/.ssh`
3. 设置正确权限：`chmod 600 ~/.ssh/*`（保留 `.pub` 为 644）

或者在新电脑上生成新密钥，然后将公钥添加到各个服务。

---

## 十一、安全最佳实践

| 实践                     | 说明                                       |
| :----------------------- | :----------------------------------------- |
| ✅ **使用 ED25519**       | 比 RSA 更安全、更快                        |
| ✅ **设置强密码短语**     | 即使私钥泄露也无法使用                     |
| ✅ **不同服务用不同密钥** | GitHub、GitLab、公司服务器分别使用不同密钥 |
| ✅ **定期更换密钥**       | 建议每年更换一次                           |
| ✅ **使用 SSH Agent**     | 避免频繁输入密码短语                       |
| ✅ **最小权限原则**       | 服务器上使用专用账号而非 root              |
| ✅ **备份私钥**           | 加密存储到密码管理器（如 1Password）       |
| ❌ **不要分享私钥**       | 私钥是身份凭证，绝不能发给任何人           |
| ❌ **不要上传私钥到云端** | 不要提交到 GitHub、网盘等                  |

---

## 十二、快速参考卡片

| 操作                    | 命令                                                    |
| :---------------------- | :------------------------------------------------------ |
| **生成 ED25519 密钥**   | `ssh-keygen -t ed25519 -C "your_email@example.com"`     |
| **生成 RSA 4096 密钥**  | `ssh-keygen -t rsa -b 4096 -C "your_email@example.com"` |
| **查看公钥**            | `cat ~/.ssh/id_ed25519.pub`                             |
| **复制公钥到剪贴板**    | `pbcopy < ~/.ssh/id_ed25519.pub` (macOS)                |
| **查看指纹**            | `ssh-keygen -lf ~/.ssh/id_ed25519.pub`                  |
| **测试 GitHub 连接**    | `ssh -T git@github.com`                                 |
| **测试 GitLab 连接**    | `ssh -T git@gitlab.com`                                 |
| **复制公钥到服务器**    | `ssh-copy-id -i ~/.ssh/id_ed25519.pub user@host`        |
| **启动 SSH Agent**      | `eval "$(ssh-agent -s)"`                                |
| **添加私钥到 Agent**    | `ssh-add ~/.ssh/id_ed25519`                             |
| **查看 Agent 中的密钥** | `ssh-add -l`                                            |
| **修复权限**            | `chmod 700 ~/.ssh && chmod 600 ~/.ssh/id_*`             |

---

按照本指南操作后，你应该能顺利生成 SSH 密钥并绑定到 GitHub、GitLab 或服务器。如果遇到任何问题，可以运行 `ssh -vT git@github.com` 查看详细日志来定位问题。