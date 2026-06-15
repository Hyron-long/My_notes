# Git 使用教程

## 一、Git 概述

### 1.1 什么是 Git

**Git** 是一个开源的**分布式版本控制系统**，由 Linus Torvalds（Linux 内核创始人）于 2005 年创建，用于高效地管理从小到大的项目版本。

### 1.2 版本控制系统的作用

| 功能         | 说明                                         |
| ------------ | -------------------------------------------- |
| **版本管理** | 记录文件的每次修改，可随时回退到任意历史版本 |
| **协作开发** | 多人同时开发同一项目，自动合并代码           |
| **分支管理** | 支持并行开发，新功能、bug修复独立进行        |
| **备份恢复** | 代码在本地和远程都有备份，防止丢失           |
| **责任追溯** | 查看谁在什么时候修改了哪一行代码             |

### 1.3 Git 与 SVN 的对比

| 特性         | Git                | SVN                |
| ------------ | ------------------ | ------------------ |
| **架构**     | 分布式             | 集中式             |
| **本地提交** | ✅ 可以             | ❌ 必须联网         |
| **分支操作** | 轻量级，切换快速   | 重量级，操作较慢   |
| **存储方式** | 内容寻址（快照）   | 增量存储           |
| **离线工作** | 几乎全部操作可离线 | 大部分操作需要联网 |
| **网络依赖** | 低                 | 高                 |

### 1.4 Git 的工作区域

Git 有三个主要的工作区域：

```
┌─────────────────────────────────────────────────────────────┐
│                        工作目录                              │
│                    （Working Directory）                     │
│                        实际文件                              │
└─────────────────────────┬───────────────────────────────────┘
                          │ git add
                          ▼
┌─────────────────────────────────────────────────────────────┐
│                      暂存区/索引                             │
│                     （Staging Area/Index）                   │
│                   下次要提交的变更                            │
└─────────────────────────┬───────────────────────────────────┘
                          │ git commit
                          ▼
┌─────────────────────────────────────────────────────────────┐
│                       本地仓库                               │
│                   （Local Repository）                       │
│                  .git 目录，永久保存                          │
└─────────────────────────┬───────────────────────────────────┘
                          │ git push
                          ▼
┌─────────────────────────────────────────────────────────────┐
│                       远程仓库                               │
│                  （Remote Repository）                       │
│                    GitHub / GitLab / Gitee                   │
└─────────────────────────────────────────────────────────────┘
```

**三者的关系**：
- **工作目录**：你正在修改的文件
- **暂存区**：准备提交的修改
- **本地仓库**：已提交的修改记录
- **远程仓库**：托管在服务器上的共享仓库

---

## 二、Git 安装与配置

### 2.1 安装 Git

**Windows**：
1. 访问 https://git-scm.com/download/win
2. 下载安装程序，按默认选项安装
3. 安装完成后，在开始菜单中找到 **Git Bash**

**macOS**：
```bash
# 使用 Homebrew 安装
brew install git

# 或使用 Xcode Command Line Tools
xcode-select --install
```

**Linux（Ubuntu/Debian）**：
```bash
sudo apt update
sudo apt install git
```

**验证安装**：
```bash
git --version
# 输出示例：git version 2.34.1
```

### 2.2 初次配置

安装后需要配置用户名和邮箱，**每次提交都会使用这些信息**：

```bash
# 设置全局用户名（推荐使用真实姓名）
git config --global user.name "Your Name"

# 设置全局邮箱
git config --global user.email "your_email@example.com"

# 查看所有配置
git config --list

# 设置默认分支名（可选）
git config --global init.defaultBranch main

# 设置文本编辑器（可选）
git config --global core.editor "code --wait"  # VS Code
```

**配置级别**：
| 级别   | 命令              | 配置文件位置     |
| ------ | ----------------- | ---------------- |
| 系统级 | `--system`        | `/etc/gitconfig` |
| 全局级 | `--global`        | `~/.gitconfig`   |
| 仓库级 | `--local`（默认） | `.git/config`    |

---

## 三、基础操作

### 3.1 初始化仓库

**创建新仓库**：
```bash
# 在当前目录初始化
git init

# 初始化为 main 分支（Git 2.28+）
git init --initial-branch=main
```

**克隆现有仓库**：
```bash
# 克隆远程仓库到本地
git clone https://github.com/username/repo.git

# 克隆到指定目录
git clone https://github.com/username/repo.git my-folder

# 克隆指定分支
git clone -b branch-name https://github.com/username/repo.git
```

### 3.2 查看状态

```bash
# 查看工作区状态（最常用）
git status

# 简洁模式
git status -s

# 查看具体修改内容
git diff                    # 工作区 vs 暂存区
git diff --cached           # 暂存区 vs 本地仓库
git diff HEAD               # 工作区 vs 本地仓库
```

### 3.3 添加和提交

```bash
# 添加文件到暂存区
git add filename.txt        # 添加单个文件
git add .                   # 添加所有修改（包括新文件）
git add --all               # 同 git add .
git add -u                  # 只添加已跟踪文件的修改

# 提交到本地仓库
git commit -m "提交说明"

# 跳过暂存区直接提交（只针对已跟踪文件）
git commit -a -m "提交说明"

# 修改最后一次提交
git commit --amend -m "新的提交说明"

# 修改最后一次提交（同时添加漏掉的文件）
git add forgotten.txt
git commit --amend --no-edit  # 不修改提交信息
```

### 3.4 查看历史

```bash
# 查看提交历史
git log

# 单行显示
git log --oneline

# 显示图形化分支
git log --oneline --graph --all

# 显示最近 n 条
git log -5

# 显示每次修改的内容
git log -p

# 查看某个文件的修改历史
git log --follow filename.txt

# 查看简短统计
git log --stat
```

### 3.5 版本回退

```bash
# 查看所有历史（包括回退后的）
git reflog

# 回退到上一个版本（HEAD^ 表示上一个）
git reset --hard HEAD^

# 回退到上两个版本
git reset --hard HEAD~2

# 回退到指定 commit
git reset --hard commit_id

# 三种 reset 模式
git reset --soft HEAD^      # 回退 commit，保留工作区和暂存区
git reset --mixed HEAD^     # 回退 commit，保留工作区，清空暂存区（默认）
git reset --hard HEAD^      # 回退 commit，清空工作区和暂存区（危险）

# 撤销工作区的修改
git checkout -- filename.txt
# 或（Git 2.23+）
git restore filename.txt

# 从暂存区撤回到工作区
git reset HEAD filename.txt
# 或
git restore --staged filename.txt
```

---

## 四、分支管理

### 4.1 分支的基本概念

**分支**是 Git 最强大的功能之一，允许你在独立的线上开发新功能，不影响主分支。

```
    main
      │
      ●─────●─────●─────●
               \
      feature   ●─────●
                         \
      bugfix             ●
```

### 4.2 分支操作命令

```bash
# 查看分支
git branch                  # 本地分支
git branch -r               # 远程分支
git branch -a               # 所有分支（本地+远程）

# 创建分支
git branch branch-name              # 基于当前分支创建
git branch branch-name commit-id    # 基于指定commit创建

# 切换分支
git checkout branch-name            # 旧语法
git switch branch-name              # Git 2.23+ 新语法

# 创建并切换
git checkout -b branch-name
git switch -c branch-name

# 合并分支
git checkout main                   # 先切换到目标分支
git merge branch-name               # 将 branch-name 合并到当前分支

# 删除分支
git branch -d branch-name           # 删除已合并的分支
git branch -D branch-name           # 强制删除（未合并）

# 重命名分支
git branch -m old-name new-name
```

### 4.3 合并方式

**1. Fast-forward 合并（快进）**：
- 条件：目标分支没有新提交
- 结果：只是移动指针，不产生合并提交

```bash
git merge --ff-only branch-name   # 只允许快进合并
```

**2. 三方合并（3-way merge）**：
- 条件：两个分支都有新提交
- 结果：产生一个新的合并提交

```bash
git merge branch-name              # 默认会创建合并提交
git merge --no-ff branch-name      # 强制创建合并提交
```

**3. Rebase（变基）**：
- 将当前分支的提交"重新播放"到目标分支上
- 产生线性历史

```bash
git checkout feature
git rebase main                    # 将 feature 变基到 main

git rebase --continue              # 解决冲突后继续
git rebase --abort                 # 放弃变基
```

**merge vs rebase**：

| 操作       | 历史记录                 | 适用场景               |
| ---------- | ------------------------ | ---------------------- |
| **merge**  | 保留分支历史，有合并节点 | 公共分支、功能分支合并 |
| **rebase** | 线性历史，更整洁         | 本地分支、清理提交历史 |

### 4.4 解决冲突

**冲突产生的原因**：两个分支修改了同一文件的同一位置。

**解决步骤**：

1. 执行合并/变基时 Git 会提示冲突：
```bash
Auto-merging file.txt
CONFLICT (content): Merge conflict in file.txt
Automatic merge failed; fix conflicts and then commit the result.
```

2. 打开冲突文件，会看到：
```
<<<<<<< HEAD
当前分支的内容
=======
要合并分支的内容
>>>>>>> branch-name
```

3. 手动修改文件，删除标记，保留想要的内容

4. 标记冲突已解决：
```bash
git add file.txt
```

5. 完成合并：
```bash
git commit                    # 合并提交
# 或
git rebase --continue         # 变基继续
```

---

## 五、远程仓库

### 5.1 远程仓库管理

```bash
# 添加远程仓库
git remote add origin https://github.com/user/repo.git

# 查看远程仓库
git remote -v

# 查看远程仓库详细信息
git remote show origin

# 修改远程仓库地址
git remote set-url origin https://github.com/user/new-repo.git

# 删除远程仓库
git remote remove origin

# 重命名远程仓库
git remote rename origin upstream
```

### 5.2 推送和拉取

```bash
# 推送到远程
git push origin main                     # 推送到远程 main 分支
git push -u origin main                  # 设置上游并推送（首次推荐）
git push origin --delete branch-name     # 删除远程分支
git push --tags                          # 推送标签

# 从远程拉取
git fetch origin                         # 只下载，不合并
git fetch origin branch-name             # 下载指定分支

git pull origin main                     # fetch + merge
git pull --rebase origin main            # fetch + rebase（推荐）

# 克隆远程仓库
git clone https://github.com/user/repo.git
git clone --depth 1 https://github.com/user/repo.git  # 浅克隆（只最近一次）
```

### 5.3 跟踪远程分支

```bash
# 查看所有分支（包括远程跟踪分支）
git branch -a

# 创建本地分支并跟踪远程分支
git checkout -b local-name origin/remote-name
# 或
git checkout --track origin/remote-name

# 设置本地分支的上游
git branch -u origin/remote-name

# 查看跟踪关系
git branch -vv
```

---

## 六、Git 常用场景

### 6.1 场景一：开始新项目

```bash
# 1. 在 GitHub 创建空仓库（不要初始化 README）

# 2. 本地初始化并关联
mkdir my-project
cd my-project
git init
echo "# My Project" >> README.md
git add README.md
git commit -m "Initial commit"

# 3. 添加远程仓库
git remote add origin https://github.com/username/my-project.git

# 4. 推送到远程
git push -u origin main
```

### 6.2 场景二：开发新功能

```bash
# 1. 从 main 创建功能分支
git checkout main
git pull origin main
git checkout -b feature/login-page

# 2. 开发并提交
git add .
git commit -m "Add login form"

# 3. 推送到远程备份
git push -u origin feature/login-page

# 4. 继续开发...
git add .
git commit -m "Add login validation"

# 5. 合并到 main（需要先更新 main）
git checkout main
git pull origin main
git merge feature/login-page

# 6. 推送到远程
git push origin main

# 7. 删除本地和远程功能分支
git branch -d feature/login-page
git push origin --delete feature/login-page
```

### 6.3 场景三：修复紧急 Bug

```bash
# 1. 从 main 创建 hotfix 分支
git checkout main
git checkout -b hotfix/critical-bug

# 2. 修复并提交
git add .
git commit -m "Fix critical bug"

# 3. 合并到 main
git checkout main
git merge hotfix/critical-bug

# 4. 也合并到 develop（如果有）
git checkout develop
git merge hotfix/critical-bug

# 5. 删除 hotfix 分支
git branch -d hotfix/critical-bug
```

### 6.4 场景四：撤销修改

```bash
# 情况1：还没有 add，撤销工作区的修改
git checkout -- filename.txt
git restore filename.txt        # Git 2.23+

# 情况2：已经 add，撤销暂存区
git reset HEAD filename.txt
git restore --staged filename.txt

# 情况3：已经 commit，撤销提交（保留修改）
git reset --soft HEAD^          # 撤销 commit，保留暂存区
git reset --mixed HEAD^         # 撤销 commit 和暂存区

# 情况4：已经 push 到远程
git revert commit_id            # 创建一个反向提交
git push origin main
```

### 6.5 场景五：暂存现场

```bash
# 保存当前工作现场
git stash save "WIP: login feature"

# 查看 stash 列表
git stash list

# 恢复最近的工作现场（恢复后不删除）
git stash apply

# 恢复最近的工作现场（恢复后删除）
git stash pop

# 恢复指定 stash
git stash apply stash@{1}

# 删除指定 stash
git stash drop stash@{0}

# 清空所有 stash
git stash clear
```

### 6.6 场景六：交互式 rebase（清理提交历史）

```bash
# 合并最近3次提交
git rebase -i HEAD~3

# 在编辑器中选择操作：
# pick   = 保留此提交
# reword = 保留但修改提交信息
# edit   = 保留但修改提交内容
# squash = 合并到前一个提交
# fixup  = 合并到前一个提交，丢弃信息
# drop   = 删除此提交

# 示例：将后两个 squash 到第一个
pick 1234567 First commit
squash 2345678 Second commit
squash 3456789 Third commit
```

---

## 七、Git 标签管理

```bash
# 创建轻量标签
git tag v1.0.0

# 创建附注标签（推荐）
git tag -a v1.0.0 -m "Release version 1.0.0"

# 对历史提交打标签
git tag -a v0.9.0 commit_id -m "Pre-release"

# 查看标签
git tag                      # 列出所有标签
git tag -l "v1.*"            # 匹配模式
git show v1.0.0              # 查看标签详情

# 推送标签到远程
git push origin v1.0.0       # 推送单个
git push --tags              # 推送所有

# 删除标签
git tag -d v1.0.0            # 删除本地
git push origin --delete v1.0.0  # 删除远程
```

---

## 八、Git 配置优化

### 8.1 常用别名

```bash
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.lg "log --oneline --graph --all"
git config --global alias.last "log -1 HEAD"
git config --global alias.unstage "reset HEAD --"
git config --global alias.undo "reset --soft HEAD^"
```

### 8.2 SSH 密钥配置

```bash
# 1. 生成 SSH 密钥
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
# 按三次回车使用默认设置

# 2. 启动 ssh-agent
eval "$(ssh-agent -s)"

# 3. 添加私钥
ssh-add ~/.ssh/id_rsa

# 4. 复制公钥
cat ~/.ssh/id_rsa.pub

# 5. 在 GitHub/GitLab 设置中添加 SSH 公钥

# 6. 测试连接
ssh -T git@github.com
```

### 8.3 .gitignore 文件

创建 `.gitignore` 文件，列出不需要 Git 跟踪的文件：

```gitignore
# 编译输出
*.class
*.o
*.exe
*.dll

# 依赖目录
node_modules/
vendor/
.env

# IDE 配置
.vscode/
.idea/
*.swp
*.swo

# 日志文件
*.log
*.tmp

# 操作系统文件
.DS_Store
Thumbs.db

# 构建产物
dist/
build/
```

---

## 九、常见问题解决

### 9.1 提交信息写错了

```bash
# 修改最后一次提交信息
git commit --amend -m "新的提交信息"

# 修改更早的提交信息
git rebase -i HEAD~n
# 将要修改的提交标记为 reword
```

### 9.2 误删分支或 commit

```bash
# 找回丢失的 commit
git reflog
# 找到丢失的 commit id，然后恢复
git checkout -b recovered-branch commit_id
```

### 9.3 撤销已经 push 的提交

```bash
# 方法1：revert（安全，推荐）
git revert commit_id
git push origin main

# 方法2：reset（会改变历史，需要强制推送，慎用）
git reset --hard commit_id
git push --force origin main
```

### 9.4 合并冲突后忘记步骤

```bash
# 查看当前状态
git status

# 如果有冲突，会显示哪些文件有冲突
# 解决冲突后：
git add .
git commit    # 或 git merge --continue
```

### 9.5 push 被拒绝（远程有新提交）

```bash
# 先拉取远程更新，再推送
git pull --rebase origin main
git push origin main

# 如果确定要覆盖远程（谨慎使用）
git push --force origin main
```

---

## 十、常用命令速查表

| 命令                  | 说明         |
| --------------------- | ------------ |
| `git init`            | 初始化仓库   |
| `git clone <url>`     | 克隆远程仓库 |
| `git add <file>`      | 添加到暂存区 |
| `git commit -m "msg"` | 提交         |
| `git status`          | 查看状态     |
| `git log`             | 查看历史     |
| `git diff`            | 查看修改     |
| `git branch`          | 查看分支     |
| `git branch <name>`   | 创建分支     |
| `git checkout <name>` | 切换分支     |
| `git merge <branch>`  | 合并分支     |
| `git pull`            | 拉取并合并   |
| `git fetch`           | 只拉取       |
| `git push`            | 推送         |
| `git stash`           | 暂存工作区   |
| `git reset`           | 回退版本     |
| `git revert`          | 反向提交     |
| `git rebase`          | 变基         |

---

## 十一、学习资源推荐

### 官方资源
- [Git 官方文档](https://git-scm.com/doc)
- [Pro Git 书籍（中文）](https://git-scm.com/book/zh/v2)

### 在线练习
- [Learn Git Branching](https://learngitbranching.js.org/) — 交互式学习分支
- [GitHub Skills](https://skills.github.com/)

### GUI 工具
- **GitHub Desktop** - 简单易用
- **SourceTree** - 功能全面
- **GitKraken** - 美观强大
- **VS Code 内置 Git** - 集成开发环境

---

**总结**：Git 的学习建议从 `init`、`add`、`commit`、`status`、`log` 开始，掌握基础后再学习分支操作。最好的学习方法就是**多用、常练**，在实际项目中积累经验。