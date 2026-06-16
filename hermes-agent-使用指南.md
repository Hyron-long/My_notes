# Hermes Agent 详细使用指南

Hermes Agent 是 Nous Research 开发的开源 AI 智能助手框架，运行在终端、消息平台和 IDE 中。
它与 Claude Code、OpenAI Codex 等属于同一类别——自主编码和任务执行代理。

官方文档：https://hermes-agent.nousresearch.com/docs/

---

## 一、安装

```bash
curl -fsSL https://hermes-agent.nousresearch.com/install.sh | bash
```

---

## 二、基本使用

### 启动交互式对话
```bash
hermes
```

### 单次查询（非交互）
```bash
hermes chat -q "法国首都叫什么？"
```

### 交互式设置向导
```bash
hermes setup
```

### 切换模型/提供商
```bash
hermes model
```

### 健康检查
```bash
hermes doctor
```

### 全局参数
```
hermes [flags] [command]

  --version, -V             显示版本
  --resume, -r SESSION      按 ID 或标题恢复会话
  --continue, -c [NAME]     按名称恢复，或恢复最近会话
  --worktree, -w            隔离 git worktree 模式（并行代理）
  --skills, -s SKILL        预加载技能（逗号分隔或重复使用）
  --profile, -p NAME        使用命名配置文件
  --yolo                    跳过危险命令审批
  --pass-session-id         在系统提示中包含会话 ID
```

---

## 三、配置管理

### 查看当前配置
```bash
hermes config
```

### 编辑配置（打开编辑器）
```bash
hermes config edit
```

### 设置配置值
```bash
hermes config set KEY VALUE
```

### 查看配置文件路径
```bash
hermes config path          # config.yaml 路径
hermes config env-path      # .env 路径
```

### 配置文件位置
```
~/.hermes/config.yaml       主配置文件
~/.hermes/.env              API 密钥和密钥
~/.hermes/skills/           已安装的技能
~/.hermes/sessions/         会话文件
~/.hermes/state.db          会话存储（SQLite + FTS5）
~/.hermes/logs/             日志
~/.hermes/auth.json         OAuth 令牌和凭证池
```

### 主要配置项

| 配置区      | 说明                                                    |
|-------------|--------------------------------------------------------|
| model       | default（模型名）、provider、base_url、api_key          |
| agent       | max_turns (90)、tool_use_enforcement                    |
| terminal    | backend (local/docker/ssh)、cwd、timeout (180)          |
| compression | enabled、threshold (0.50)、target_ratio (0.20)          |
| display     | skin、tool_progress、show_reasoning、show_cost           |
| stt         | enabled、provider (local/groq/openai/mistral)            |
| tts         | provider (edge/elevenlabs/openai/minimax/mistral/neutts) |
| memory      | memory_enabled、user_profile_enabled、provider           |
| security    | tirith_enabled、website_blocklist                        |
| delegation  | model、provider、max_iterations (50)                     |
| auxiliary   | 各辅助任务的 provider/model/base_url/api_key             |

---

## 四、模型与提供商

### 支持的提供商（20+）

| 提供商             | 认证方式   | 环境变量                  |
|--------------------|-----------|--------------------------|
| OpenRouter         | API key   | OPENROUTER_API_KEY        |
| Anthropic          | API key   | ANTHROPIC_API_KEY         |
| Nous Portal        | OAuth     | hermes auth               |
| OpenAI Codex       | OAuth     | hermes auth               |
| GitHub Copilot     | Token     | COPILOT_GITHUB_TOKEN      |
| Google Gemini      | API key   | GOOGLE_API_KEY            |
| DeepSeek           | API key   | DEEPSEEK_API_KEY          |
| xAI / Grok         | API key   | XAI_API_KEY               |
| Hugging Face       | Token     | HF_TOKEN                  |
| Alibaba/DashScope  | API key   | DASHSCOPE_API_KEY         |
| Kimi / Moonshot    | API key   | KIMI_API_KEY              |
| MiniMax            | API key   | MINIMAX_API_KEY           |
| 自定义端点         | Config    | model.base_url + api_key  |

### 切换模型
```bash
hermes model                # 交互式选择
hermes config set model.default "模型名称"
hermes config set model.provider "提供商名称"
```

### 凭证管理
```bash
hermes auth                 # 交互式凭证向导
hermes auth add PROVIDER    # 添加凭证
hermes auth list            # 列出凭证
hermes auth remove PROVIDER # 移除凭证
```

---

## 五、工具集（Toolsets）

### 管理工具
```bash
hermes tools                # 交互式启用/禁用
hermes tools list           # 列出所有工具及状态
hermes tools enable NAME    # 启用工具集
hermes tools disable NAME   # 禁用工具集
```

### 可用工具集

| 工具集           | 功能说明                        |
|------------------|--------------------------------|
| web              | 网页搜索和内容提取              |
| search           | 仅网页搜索                      |
| browser          | 浏览器自动化                    |
| terminal         | Shell 命令和进程管理            |
| file             | 文件读写搜索编辑                |
| code_execution   | 沙箱 Python 执行               |
| vision           | 图像分析                        |
| image_gen        | AI 图像生成                     |
| video            | 视频分析和生成                  |
| tts              | 文本转语音                      |
| skills           | 技能浏览和管理                  |
| memory           | 持久化跨会话记忆                |
| session_search   | 搜索过去的对话                  |
| delegation       | 子代理任务委派                  |
| cronjob          | 定时任务管理                    |
| clarify          | 向用户提问澄清问题              |
| todo             | 会话内任务规划与跟踪            |
| spotify          | Spotify 播放控制                |
| homeassistant    | 智能家居控制                    |
| discord          | Discord 集成                    |

**注意：** 工具更改在 `/reset`（新会话）后生效，不会在对话中途应用。

---

## 六、技能系统（Skills）

技能是 Hermes 的程序记忆——可重用的工作流程和方法。

### 技能管理
```bash
hermes skills list          # 列出已安装技能
hermes skills search QUERY  # 搜索技能
hermes skills install ID    # 安装技能
hermes skills browse        # 浏览所有可用技能
hermes skills check         # 检查更新
hermes skills update        # 更新过时技能
hermes skills uninstall N   # 卸载技能
```

### 会话内加载技能
```
/skill <name>               # 加载技能到当前会话
/reload-skills              # 重新扫描技能目录
```

### Curator（技能生命周期管理）
```bash
hermes curator status       # 查看状态
hermes curator run          # 手动运行维护
hermes curator pin NAME     # 保护技能不被自动删除
hermes curator unpin NAME   # 取消保护
hermes curator archive      # 归档过时技能
```

---

## 七、斜杠命令（会话内）

### 会话控制
```
/new (/reset)        开始新会话
/clear               清屏 + 新会话（CLI）
/retry               重新发送最后一条消息
/undo                撤销最后一次交互
/title [name]        命名会话
/compress            手动压缩上下文
/stop                终止后台进程
/background <prompt> 在后台运行提示
/queue <prompt>      排队到下一轮
/steer <prompt>      在下次工具调用后注入消息
/agents (/tasks)     查看活跃代理和任务
/resume [name]       恢复命名会话
/goal [text|sub]     设置持续目标
```

### 配置
```
/config              显示配置
/model [name]        显示或切换模型
/personality [name]  设置个性
/reasoning [level]   设置推理级别 (none|minimal|low|medium|high|xhigh)
/verbose             循环：off → new → all → verbose
/voice [on|off|tts]  语音模式
/yolo                切换审批绕过
/skin [name]         切换主题
```

### 工具与技能
```
/tools               管理工具
/skills              搜索/安装技能
/skill <name>        加载技能
/cron                管理定时任务
/curator [sub]       后台技能维护
/plugins             列出插件
```

### 信息
```
/help                显示命令列表
/usage               Token 使用量
/insights [days]     使用分析
/status              会话信息
/profile             活跃配置文件信息
/debug               上传调试报告
```

### 退出
```
/quit (/exit, /q)    退出
```

---

## 八、会话管理

```bash
hermes sessions list        # 列出最近会话
hermes sessions browse      # 交互式选择器
hermes sessions export OUT  # 导出到 JSONL
hermes sessions rename ID T # 重命名会话
hermes sessions delete ID   # 删除会话
hermes sessions prune       # 清理旧会话
hermes sessions stats       # 存储统计
```

---

## 九、定时任务（Cron Jobs）

### 管理定时任务
```bash
hermes cron list            # 列出任务
hermes cron create SCHED    # 创建（'30m'、'every 2h'、'0 9 * * *'）
hermes cron edit ID         # 编辑
hermes cron pause ID        # 暂停
hermes cron resume ID       # 恢复
hermes cron run ID          # 立即运行
hermes cron remove ID       # 删除
hermes cron status          # 调度器状态
```

### 调度格式
- 时长：`"30m"`（每30分钟）、`"2h"`（每2小时）
- 短语：`"every monday 9am"`
- Cron 表达式：`"0 9 * * *"`（每天上午9点）
- ISO 时间戳：`"2026-06-01T09:00:00"`（一次性）

### 会话内管理
```
/cron                管理定时任务
```

---

## 十、配置文件（Profiles）

Profiles 允许运行多个独立的 Hermes 实例，各自拥有独立的配置、会话、技能和记忆。

```bash
hermes profile list         # 列出所有配置
hermes profile create NAME  # 创建
hermes profile use NAME     # 设为默认
hermes profile delete NAME  # 删除
hermes profile show NAME    # 显示详情
hermes profile export NAME  # 导出为 tar.gz
hermes profile import FILE  # 从压缩包导入
```

使用时加 `-p` 参数：
```bash
hermes -p work              # 使用 "work" 配置
```

---

## 十一、Gateway（消息平台网关）

### 管理 Gateway
```bash
hermes gateway run          # 前台启动
hermes gateway install      # 安装为后台服务
hermes gateway start        # 启动服务
hermes gateway stop         # 停止服务
hermes gateway restart      # 重启服务
hermes gateway status       # 检查状态
hermes gateway setup        # 配置平台
```

### 支持的平台
Telegram、Discord、Slack、WhatsApp、Signal、Email、SMS、Matrix、
Mattermost、Home Assistant、DingTalk、飞书、企业微信、iMessage、
微信、API Server、Webhooks

---

## 十二、MCP 服务器

```bash
hermes mcp serve            # 将 Hermes 作为 MCP 服务器运行
hermes mcp add NAME         # 添加 MCP 服务器
hermes mcp remove NAME      # 移除
hermes mcp list             # 列出已配置的服务器
hermes mcp test NAME        # 测试连接
```

---

## 十三、记忆系统

Hermes 有持久化记忆，跨会话保留信息：
- 用户偏好和习惯
- 环境信息
- 经验教训

### 管理记忆
```bash
hermes memory setup         # 配置记忆提供商
hermes memory status        # 查看状态
hermes memory off           # 关闭记忆
```

会话内记忆由 AI 自动管理——当它学到重要信息时会主动保存。

---

## 十四、语音功能

### STT（语音转文字）
支持提供商：
1. 本地 faster-whisper（免费）：`pip install faster-whisper`
2. Groq Whisper（免费层）：设置 `GROQ_API_KEY`
3. OpenAI Whisper（付费）：设置 `VOICE_TOOLS_OPENAI_KEY`

### TTS（文字转语音）
| 提供商      | 环境变量                | 免费？ |
|-------------|------------------------|--------|
| Edge TTS    | 无需                    | 是     |
| ElevenLabs  | ELEVENLABS_API_KEY     | 有免费层|
| OpenAI      | VOICE_TOOLS_OPENAI_KEY | 付费   |
| MiniMax     | MINIMAX_API_KEY        | 付费   |

语音命令：`/voice on`（语音对话）、`/voice tts`（总是语音）、`/voice off`

---

## 十五、安全与隐私

### 密钥脱敏（默认开启）
```bash
hermes config set security.redact_secrets true    # 开启
hermes config set security.redact_secrets false   # 关闭（调试用）
```

### 命令审批
```bash
hermes config set approvals.mode manual   # 手动审批（默认）
hermes config set approvals.mode smart    # 智能审批
hermes config set approvals.mode off      # 跳过所有审批
```

单次跳过：`hermes --yolo`

### PII 脱敏
```bash
hermes config set privacy.redact_pii true    # 开启
hermes config set privacy.redact_pii false   # 关闭（默认）
```

---

## 十六、常见问题排查

### 辅助模型不工作
如果辅助任务（标题生成、压缩、视觉分析等）失败，需要配置辅助 provider：
```bash
hermes config set auxiliary.title_generation.provider <你的provider>
hermes config set auxiliary.title_generation.model <模型名>
hermes config set auxiliary.title_generation.base_url <接口地址>
hermes config set auxiliary.title_generation.api_key <你的key>
```

### 工具不可用
1. `hermes tools` — 检查是否启用
2. 检查 `.env` 中是否有需要的环境变量
3. `/reset` 后生效

### 修改不生效
- 工具/技能：`/reset` 开始新会话
- 配置更改：Gateway 中 `/restart`，CLI 中退出重启
- 代码更改：重启 CLI 或 Gateway 进程

### 技能不显示
1. `hermes skills list` — 确认已安装
2. `hermes skills config` — 检查平台启用状态
3. 显式加载：`/skill name` 或 `hermes -s name`

### Gateway 问题
检查日志：
```bash
grep -i "failed to send\|error" ~/.hermes/logs/gateway.log | tail -20
```

---

## 十七、Webhook

```bash
hermes webhook subscribe NAME   # 创建路由 /webhooks/<name>
hermes webhook list             # 列出订阅
hermes webhook remove NAME      # 移除
hermes webhook test NAME        # 发送测试 POST
```

---

## 十八、子代理委派

在会话内可以使用 delegate_task 工具将任务委派给子代理并行处理。

通过终端启动独立 Hermes 实例：
```bash
# 单次查询
hermes chat -q "研究 GRPO 论文并写总结到 ~/research/grpo.md"

# 交互式（通过 tmux）
tmux new-session -d -s agent1 -x 120 -y 40 'hermes'
sleep 8 && tmux send-keys -t agent1 '构建 FastAPI 认证服务' Enter
```

---

## 十九、常用工作流示例

### 开发工作流
```bash
hermes                          # 启动
# 在对话中：
/skill github-pr-workflow       # 加载 PR 工作流技能
# 让 AI 帮你创建分支、写代码、提交 PR
```

### 研究工作流程
```bash
hermes -s arxiv,youtube-content # 预加载研究技能
# 让 AI 搜索论文、获取 YouTube 视频摘要
```

### 自动化任务
```bash
hermes cron create "0 9 * * *"   # 每天上午9点执行
# 配置定时任务内容
```

---

## 二十、快捷键（CLI 内）

- Ctrl+B：开始语音录制
- Ctrl+Enter：插入换行（某些终端）
- Tab：自动补全
- /help：查看完整命令列表

---

## 二十一、其他有用命令

```bash
hermes insights [--days N]      # 使用分析
hermes update                   # 更新到最新版本
hermes pairing list/approve/revoke  # DM 授权
hermes plugins list/install/remove  # 插件管理
hermes completion bash|zsh      # Shell 自动补全
hermes acp                      # ACP 服务器（IDE 集成）
hermes uninstall                # 卸载 Hermes
```

---

最后更新：2026-06-16
基于 Hermes Agent 官方文档整理
