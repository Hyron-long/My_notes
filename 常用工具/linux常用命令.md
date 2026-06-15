## Linux 命令速查表

| 类别       | 命令                    | 说明            | 示例                                                                      |
| :------- | :-------------------- | :------------ | :---------------------------------------------------------------------- |
| **文件操作** | `ls`                  | 列出目录内容        | `ls -la` (显示所有文件，含详情)                                                   |
|          | `cd`                  | 切换目录          | `cd /home/user`                                                         |
|          | `pwd`                 | 显示当前路径        | `pwd`                                                                   |
|          | `cp`                  | 复制文件/目录       | `cp file1.txt file2.txt`                                                |
|          | `mv`                  | 移动/重命名        | `mv old.txt new.txt`                                                    |
|          | `rm`                  | 删除文件/目录       | `rm -rf dir/` (强制递归删除)                                                  |
|          | `mkdir`               | 创建目录          | `mkdir -p parent/child` (递归创建)                                          |
|          | `touch`               | 创建空文件/更新时间戳   | `touch newfile.txt`                                                     |
| **文本处理** | `cat`                 | 查看完整文件        | `cat file.txt`                                                          |
|          | `more`/`less`         | 分页查看          | `less largefile.log` (按q退出)                                             |
|          | `head`/`tail`         | 查看头/尾N行       | `tail -f app.log` (实时跟踪日志)                                              |
|          | `grep`                | 搜索文本          | `grep "error" *.log`                                                    |
|          | `sed`                 | 流编辑器(替换)      | `sed 's/old/new/g' file.txt`                                            |
|          | `awk`                 | 文本处理工具        | `awk '{print $1}' file.txt` (打印第一列)                                     |
| **权限管理** | `chmod`               | 修改权限          | `chmod 755 script.sh`                                                   |
|          | `chown`               | 修改所有者         | `chown user:group file.txt`                                             |
| **进程管理** | `ps`                  | 查看进程          | `ps aux` (所有进程详情)                                                       |
|          | `top`/`htop`          | 动态监控          | `htop` (更友好的交互界面)                                                       |
|          | `kill`                | 终止进程          | `kill -9 PID` (强制终止)                                                    |
|          | `jobs`/`bg`/`fg`      | 后台作业管理        | `Ctrl+Z` (挂起), `bg` (后台运行)                                              |
| **网络相关** | `netstat`             | 网络状态          | `netstat -tuln` (监听端口)                                                  |
|          | `ss`                  | socket统计(更现代) | `ss -tuln`                                                              |
|          | `curl`                | HTTP请求        | `curl -I https://example.com`                                           |
|          | `wget`                | 下载文件          | `wget https://example.com/file.zip`                                     |
|          | `ping`                | 连通性测试         | `ping google.com`                                                       |
| **压缩打包** | `tar`                 | 打包/解包         | `tar -czvf archive.tar.gz dir/` (压缩)<br>`tar -xzvf archive.tar.gz` (解压) |
| **系统信息** | `df`                  | 磁盘空间          | `df -h` (人类可读格式)                                                        |
|          | `du`                  | 目录大小          | `du -sh /var/log`                                                       |
|          | `free`                | 内存使用          | `free -h`                                                               |
|          | `uname`               | 系统版本          | `uname -a`                                                              |
| **包管理**  | `apt` (Debian/Ubuntu) | 安装软件          | `sudo apt update && sudo apt install vim`                               |
|          | `yum` (CentOS/RHEL)   | 安装软件          | `sudo yum install vim`                                                  |

## 核心概念详解

### 1. 文件系统导航

```bash
# 绝对路径 vs 相对路径
cd /var/log          # 绝对路径，从根目录开始
cd ../etc            # 相对路径，上一级目录的etc文件夹

# 特殊目录
.                    # 当前目录
..                   # 上一级目录
~                    # 用户主目录
-                    # 上一次所在目录 (cd - 可以来回切换)

# 查看目录树(需要安装tree)
tree /home          # 以树形结构显示
tree -L 2 /home     # 只显示2层深度
```

### 2. 管道和重定向

这是Linux命令组合的核心思想：

```bash
# 重定向 (> 覆盖, >> 追加)
ls > files.txt       # 将ls输出保存到文件
echo "hello" >> log.txt  # 追加内容

# 管道 (|) - 前一个命令的输出作为后一个命令的输入
ps aux | grep mysql          # 查找mysql进程
cat access.log | grep "404" | wc -l  # 统计404错误的次数

# 输入重定向
sort < unsorted.txt          # 从文件读入数据排序

# 组合使用
find /var/log -name "*.log" | xargs grep "ERROR"  # 在多个日志文件中搜索ERROR
```

### 3. 文本处理的黄金组合

```bash
# 日志分析实战 - 统计每个IP的访问次数
cat access.log | awk '{print $1}' | sort | uniq -c | sort -rn | head -10

# 解析命令：
# awk '{print $1}'   # 提取第一列(通常是IP)
# sort              # 排序使相同IP相邻
# uniq -c           # 去重并计数
# sort -rn          # 按计数逆向排序
# head -10          # 显示前10行

# 查找并替换
sed -i 's/old_text/new_text/g' file.txt  # 直接修改文件

# 提取特定字段
ps aux | awk '$3 > 10 {print $2, $11}'   # 打印CPU使用率>10%的进程ID和命令
```

### 4. 权限系统深入

```bash
# 权限数字表示法 (rwx = 421)
# 755 = rwxr-xr-x (所有者全权，组和其他人读执行)
chmod 755 script.sh

# 权限符号表示法
chmod u+x script.sh      # 给所有者添加执行权限
chmod g-w file.txt       # 移除组的写权限
chmod o=r file.txt       # 只给其他人读权限

# umask - 新文件的默认权限
umask 022                # 新文件默认644，新目录默认755

# 特殊权限
chmod +t /tmp            # Sticky bit (只有所有者能删除)
chmod g+s /project       # SetGID (新文件继承组)
```

### 5. 进程管理与监控

```bash
# 查看进程树
pstree -p                # 显示进程树和PID

# 更友好的top - htop (需要安装)
htop                     # 支持鼠标操作，F9杀死进程

# 实时查看日志并过滤
tail -f app.log | grep -E "ERROR|WARNING" --color=always

# 后台运行并保持
nohup ./long_running.sh > output.log 2>&1 &  # 退出终端后仍运行
disown %1                # 将已运行的任务从shell剥离

# 系统负载
uptime                   # 显示负载平均值(1,5,15分钟)
```

### 6. 网络调试实用技巧

```bash
# 查看端口占用
lsof -i :8080            # 查看8080端口谁在用
netstat -tunlp | grep 8080

# 快速HTTP请求
curl -X POST -H "Content-Type: application/json" -d '{"key":"value"}' https://api.example.com/endpoint

# 文件下载带进度条
wget -c --progress=bar:force https://largefile.com/ubuntu.iso

# 测试带宽
iperf3 -c server_ip      # 需要两边都安装iperf3

# DNS查询
dig google.com           # 详细DNS信息
nslookup google.com      # 简单查询
```

## 实用技巧

### 快捷键
```bash
Ctrl + C      # 终止当前命令
Ctrl + Z      # 挂起当前命令
Ctrl + D      # 退出当前shell/EOF
Ctrl + L      # 清屏 (相当于clear)
Ctrl + R      # 搜索历史命令
Ctrl + A/E    # 移动到行首/行尾
Ctrl + U/K    # 删除光标前/后所有内容
!!            # 执行上一条命令
!$            # 上一条命令的最后一个参数
```

### 别名定义
```bash
# 临时别名
alias ll='ls -alF'
alias gs='git status'

# 永久保存到 ~/.bashrc 或 ~/.zshrc
echo "alias ll='ls -alF'" >> ~/.bashrc
source ~/.bashrc
```

### 查找文件的高效方式
```bash
# find vs locate
find /home -name "*.txt" -type f -size +1M  # 实时搜索，支持条件
locate myfile.txt                            # 基于索引，速度更快(需要updatedb)

# 查找并执行
find . -name "*.tmp" -delete                 # 删除所有tmp文件
find . -name "*.log" -exec grep "error" {} \;  # 在找到的文件中搜索
```

## 安全注意事项

1. **`rm -rf` 要极其小心** - 没有回收站，删了就没了
2. **避免以root运行不信任的脚本**
3. **检查命令来源** - 不要curl管道执行未知脚本 (避免 `curl ... | sh`)
4. **谨慎使用 `chmod 777`** - 这会开放所有权限

## 实战演练场景

### 场景1: 快速定位大文件
```bash
# 找出/var下最大的10个文件
find /var -type f -exec du -h {} + | sort -rh | head -10

# 或者更简洁的方式
du -ah /var | sort -rh | head -10
```

### 场景2: 批量重命名文件
```bash
# 将所有jpg后缀改为JPG
for file in *.jpg; do mv "$file" "${file%.jpg}.JPG"; done

# 使用rename命令（更强大）
rename 's/\.jpg$/\.JPG/' *.jpg
```

### 场景3: 监控日志并发送警报
```bash
tail -f app.log | while read line; do
    if echo "$line" | grep -q "FATAL"; then
        echo "$line" | mail -s "Fatal Error Detected" admin@example.com
    fi
    echo "$line"
done
```

## 学习资源

- **手册页**: `man command` (例如 `man ls`)
- **TLDR**: 需要安装 (`tldr command`) - 提供简洁的示例
- **练习**: 在 `/tmp` 或虚拟机里安全地练习

掌握这些命令需要实践，建议在日常工作中多使用终端而非GUI工具。遇到不会的命令时，先用 `--help` 或 `man` 查看，很快就能形成肌肉记忆。
