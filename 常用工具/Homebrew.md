# Homebrew

---

### 一、 Homebrew 

**Homebrew** 是一款开源的软件包管理工具，由 Max Howell 开发。它的主要作用是在 macOS（以及 Linux）系统上**便捷地安装、升级、卸载和管理软件**。

在 Linux 上，它有时被称为 Linuxbrew，但目前已完全合并进 Homebrew 项目。

### 二、 安装指南

Homebrew 的安装主要依赖终端，整个安装过程可能需要几分钟。

#### 1. macOS / Linux 通用安装
打开终端（Terminal），粘贴以下命令并回车：
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
**注意**：
- 安装过程中需要输入开机密码。
- 它会自动安装苹果的 Command Line Tools（macOS 上）或编译工具（Linux 上）。

#### 2. 国内加速安装（推荐）
由于 GitHub 在国内访问较慢，建议使用国内镜像脚本：
```bash
/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"
```

#### 3. 配置环境变量（仅限 Linux）
Linux 安装完成后，通常不会自动加载命令，需要手动配置：
```bash
echo 'eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"' >> ~/.bashrc
# 如果使用 Zsh，则写入 ~/.zshrc
eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"
```

#### 4. 验证安装
完全退出并重新打开终端（重要），输入：
```bash
brew -v
# 或进行健康诊断
brew doctor
```
如果显示版本号且没有严重的警告，说明安装成功。

---

### 三、 核心概念

在使用前，需要理解 Homebrew 的两个核心概念：

1. **Formulae (软件配方)**：通常指**命令行工具**、开发库和后端服务（如 `wget`, `git`, `mysql`, `python`）。
2. **Casks (酒桶)**：指 **macOS 图形界面应用**（GUI 应用程序），例如 Google Chrome、Visual Studio Code、WeChat 等。

*在早期的 Homebrew 版本中，Cask 命令为 `brew cask`，现在已完全集成，统一使用 `brew` 命令。*

---

### 四、 常用命令详解

这是日常使用最频繁的部分。

| 操作     | Formulae (命令行工具) | Casks (图形应用)                      | 说明                         |
| :------- | :-------------------- | :------------------------------------ | :--------------------------- |
| **搜索** | `brew search wget`    | `brew search google-chrome`           | 支持模糊搜索                 |
| **安装** | `brew install wget`   | `brew install --cask google-chrome`   | 安装 GUI 软件必须带 `--cask` |
| **卸载** | `brew uninstall wget` | `brew uninstall --cask google-chrome` | 清除软件包                   |
| **列出** | `brew list`           | `brew list --cask`                    | 查看已装列表                 |
| **升级** | `brew upgrade wget`   | `brew upgrade --cask google-chrome`   | 更新单个软件                 |
| **信息** | `brew info wget`      | `brew info --cask google-chrome`      | 查看依赖和安装路径           |

**实用技巧**：
其实现在的 Homebrew 非常智能，很多 GUI 软件也可以直接用 `brew install google-chrome`，它会自动识别为 Cask 安装。

#### 1. 更新所有软件
这是一个比较常用的组合命令，用于更新 Homebrew 自身索引以及所有软件：
```bash
brew update   # 更新软件源列表
brew upgrade  # 将所有已安装软件升级到最新版
```

#### 2. 清理磁盘空间
Homebrew 会保留旧版本软件，时间久了会占用大量磁盘空间：
```bash
brew cleanup          # 清理所有旧版本和缓存
brew cleanup -n       # 预览会清理哪些内容（不实际删除）
brew cleanup wget     # 只清理特定软件
```

---

### 五、 进阶技巧：批量管理 (Brewfile)

如果你需要在多台电脑上同步开发环境，或者换新电脑后不想一个个手动安装软件，Brewfile 会是很好的选择。

#### 1. 导出当前配置
将你电脑上**所有**通过 Homebrew 安装的命令行软件和 GUI 软件导出为一个文件：
```bash
brew bundle dump
```
执行后，当前目录会生成一个 `Brewfile` 文件。

#### 2. 在新电脑上批量恢复
将 `Brewfile` 拷贝到新电脑，在同级目录下执行：
```bash
brew bundle install
```
Homebrew 会自动读取文件里的清单，把软件一个一个安装好，不需要人工干预。

---

### 六、 软件源管理 (Tap)

Homebrew 的默认源（仓库）里包含大部分主流软件。如果你想安装一些非官方的特定版本或小众软件，可以使用 `tap`。

-   **添加第三方源**：
    ```bash
    brew tap homebrew/cask-versions  # 包含旧版本软件
    ```
-   **查看已添加的源**：
    ```bash
    brew tap
    ```

---

### 七、 常见问题与维护

**1. 运行 `brew doctor`**
当感觉 Homebrew 行为异常时，不要只是卸载重装。先运行这个命令，它会给出具体的修复建议。

**2. 如何卸载 Homebrew？**
如果你不再需要使用 Homebrew，可以使用官方卸载脚本：
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/uninstall.sh)"
```

**3. 安装某个软件很慢或下载中断**
这通常是因为 GitHub 或境外 CDN 被墙。解决方法：
-   使用上面提到的国内自动化安装脚本（它通常会帮你配置好中科大的镜像源）。
-   手动替换镜像源（搜索“Homebrew 切换清华源”）。

**4. Linux 上的权限问题**
在 Linux 上使用 Homebrew **不需要**加 `sudo`。如果安装软件时提示权限错误，说明你的配置不正确，请检查环境变量设置。

### 总结
| 场景           | 命令                                                      |
| :------------- | :-------------------------------------------------------- |
| **安装应用**   | `brew install <包名>` 或 `brew install --cask <应用名>`   |
| **换电脑迁移** | 旧电脑 `brew bundle dump` -> 新电脑 `brew bundle install` |
| **日常维护**   | `brew update && brew upgrade` 然后 `brew cleanup`         |