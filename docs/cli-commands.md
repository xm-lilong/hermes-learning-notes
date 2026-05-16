# Hermes CLI 命令完全手册

> 所有命令按使用场景分类。你可以不用，但得知道有什么。

---

## 一、会话管理

### `hermes` / `hermes chat`
启动交互式对话。这是你最常用的入口。
```bash
hermes                    # 进入交互模式
hermes -c                 # 恢复最近一次会话
hermes -r <session_id>    # 恢复指定会话
hermes -s novel-writing   # 启动时预加载技能
```

### `hermes chat -q`
单次提问，不进入交互模式。适合脚本调用或简单查询。
```bash
hermes chat -q "现在几点了"
hermes chat -q "查一下 /opt 磁盘占用" -t terminal,file
```

### 会话恢复
```bash
hermes --continue          # 恢复最近会话（等同于 -c）
hermes --resume <id>       # 按 ID 恢复
hermes --resume <name>     # 按标题模糊匹配恢复
```

### 会话管理命令
```bash
hermes sessions list       # 列出最近会话
hermes sessions browse     # 交互式选择会话
hermes sessions stats      # 会话存储统计
hermes sessions export <文件>  # 导出为 JSONL
hermes sessions rename <id> <名>  # 重命名
hermes sessions delete <id>  # 删除会话
hermes sessions prune      # 清理旧会话（--older-than 30）
```

### 会话内斜杠命令
```
/new         重置会话（清空上下文）
/clear       清屏 + 重置
/retry       重发上条消息
/undo        撤销上一次对话
/title xxx   给会话命名
/compress    手动压缩上下文
/stop        终止后台进程
/rollback N  回退文件系统快照
/history     查看对话历史
/save        保存对话到文件
/quit        退出
```

---

## 二、模型与配置

### 切换模型
```bash
hermes model               # 交互式选择模型/提供商
hermes chat -m deepseek-v4-pro  # 指定模型启动
hermes chat --provider deepseek  # 指定提供商
```

### 配置管理
```bash
hermes config              # 查看当前完整配置
hermes config edit         # 用 $EDITOR 打开 config.yaml
hermes config path         # 显示 config.yaml 路径
hermes config env-path     # 显示 .env 文件路径
hermes config set <key> <val>  # 设置配置项
hermes config check        # 检查配置缺失/过期项
hermes config migrate      # 更新配置到最新版本
```

### 常用 config set 示例
```bash
hermes config set model.default deepseek-v4-pro
hermes config set agent.max_turns 120
hermes config set terminal.timeout 300
hermes config set display.personality concise
hermes config set approvals.mode smart
hermes config set compression.enabled true
```

### 认证
```bash
hermes login --provider nous    # Nous Research OAuth 登录
hermes login --provider openai-codex  # OpenAI Codex OAuth
hermes logout                    # 清除所有认证
hermes auth list                 # 查看凭据池
hermes auth add                  # 交互式添加 API key
hermes auth remove <provider> <index>  # 删除凭据
hermes auth reset <provider>     # 重置耗尽状态
```

### 会话内模型命令
```
/model          查看当前模型
/model xxx      切换到指定模型
/reasoning low  设置推理深度 (none/minimal/low/medium/high/xhigh)
/verbose        切换详细输出
/yolo           跳过危险命令确认
/personality xxx 切换人格
```

---

## 三、工具与技能

### 工具管理
```bash
hermes tools                # 交互式工具开关（curses UI）
hermes tools list           # 列出所有工具及状态
hermes tools enable terminal  # 启用工具集
hermes tools disable browser  # 禁用工具集
```

### 可用工具集
| 工具集 | 提供的能力 |
|--------|-----------|
| `terminal` | Shell 命令、进程管理 |
| `file` | 文件读写搜索修改 |
| `web` | 网页搜索和内容提取 |
| `browser` | 浏览器自动化 |
| `vision` | 图片分析 |
| `delegation` | 子代理任务委派 |
| `cronjob` | 定时任务管理 |
| `memory` | 跨会话记忆 |
| `session_search` | 搜索历史对话 |
| `skills` | 技能浏览管理 |
| `messaging` | 跨平台消息发送 |
| `todo` | 任务规划跟踪 |
| `clarify` | 向用户提问 |

### 技能管理
```bash
hermes skills list          # 列出已安装技能
hermes skills search <关键词>  # 搜索技能市场
hermes skills install <id>  # 安装技能
hermes skills inspect <id>  # 预览技能内容
hermes skills uninstall <名>  # 卸载技能
hermes skills update        # 更新过期技能
hermes skills check         # 检查更新
hermes skills browse        # 浏览所有可用技能
hermes skills publish <路径>  # 发布技能到注册表
hermes skills tap add <repo>  # 添加 GitHub 仓库为技能源
```

### 会话内
```
/tools          管理工具
/toolsets       列出工具集
/skills         搜索安装技能
/skill <name>   加载指定技能到当前会话
```

---

## 四、消息平台（Gateway）

### 网关管理
```bash
hermes gateway run          # 前台运行网关
hermes gateway install      # 安装为系统服务（开机自启）
hermes gateway start        # 启动服务
hermes gateway stop         # 停止服务
hermes gateway restart      # 重启服务
hermes gateway status       # 查看状态
hermes gateway setup        # 交互式配置平台
```

### 支持的平台
Telegram、Discord、Slack、WhatsApp、Signal、Email、SMS、Matrix、飞书、微信、钉钉、企业微信、iMessage（BlueBubbles）、Open WebUI

### 平台特定配置
```bash
# 每个平台有独立的环境变量
FEISHU_APP_ID=xxx
FEISHU_APP_SECRET=xxx
WEIXIN_ACCOUNT_ID=xxx
TELEGRAM_BOT_TOKEN=xxx
DISCORD_BOT_TOKEN=xxx
```

### 网关内命令
```
/approve       批准待执行命令
/deny          拒绝待执行命令
/restart       重启网关
/sethome       设当前聊天为 home channel
/update        更新 Hermes
/platforms     查看平台连接状态
/status        会话信息
```

---

## 五、定时任务（Cron）

### 任务管理
```bash
hermes cron list            # 列出所有任务（--all 包含已禁用）
hermes cron create <schedule>  # 创建任务
hermes cron edit <id>       # 编辑任务
hermes cron pause <id>      # 暂停
hermes cron resume <id>     # 恢复
hermes cron run <id>        # 触发下次执行（推进 next_run_at）
hermes cron remove <id>     # 删除任务
hermes cron status          # 调度器状态
```

### 调度表达式
```bash
hermes cron create '*/30 * * * *'   # 每30分钟（推荐）
hermes cron create 'every 1h'       # 每1小时
hermes cron create '0 9 * * *'      # 每天9点
hermes cron create '0 0 * * 0'      # 每周日0点
```

### 创建示例
```bash
hermes cron create '*/30 * * * *' \
  -q '检查 /opt/data 磁盘使用率' \
  --deliver feishu
```

---

## 六、多 Profile

用于运行多个完全独立的 Hermes 实例（不同项目、不同配置）。

```bash
hermes profile list           # 列出所有 profile
hermes profile create <名>    # 创建新 profile
hermes profile use <名>       # 设为默认
hermes profile delete <名>    # 删除
hermes profile show <名>      # 查看详情
hermes profile rename <旧> <新>  # 重命名
hermes profile export <名>    # 导出为 tar.gz
hermes profile import <文件>  # 从归档导入
```

### 使用场景
```bash
# 小说项目 profile（独立 skills、memory、配置）
hermes profile create novel-writer --clone
hermes -p novel-writer

# 运维 profile
hermes profile create devops
hermes -p devops
```

---

## 七、MCP 服务器

Model Context Protocol — 让 Hermes 连接外部工具服务。

```bash
hermes mcp add <名> --url <地址>     # 添加 HTTP MCP 服务
hermes mcp add <名> --command <命令>  # 添加进程 MCP 服务
hermes mcp remove <名>               # 移除
hermes mcp list                      # 列出已配置
hermes mcp test <名>                 # 测试连接
hermes mcp configure <名>            # 切换工具选择
hermes mcp serve                     # 将 Hermes 作为 MCP 服务暴露
```

---

## 八、Webhook

接收外部 HTTP 请求触发 Agent 执行。

```bash
hermes webhook subscribe <名>   # 创建路由 /webhooks/<名>
hermes webhook list             # 列出订阅
hermes webhook remove <名>      # 移除
hermes webhook test <名>        # 发送测试 POST
```

### 使用场景
```bash
# 创建后，外部可以通过 POST 触发
curl -X POST http://localhost:9119/webhooks/my-hook \
  -H "Content-Type: application/json" \
  -d '{"message": "部署完成，检查一下"}'
```

---

## 九、系统维护

### 健康检查
```bash
hermes doctor               # 检查依赖和配置
hermes doctor --fix         # 自动修复问题
hermes status               # 组件状态总览
hermes status --all         # 详细信息
```

### 更新升级
```bash
hermes update               # 更新到最新版本
hermes setup                # 交互式设置向导
hermes setup model          # 只设置模型部分
hermes setup gateway        # 只设置网关部分
```

### 使用统计
```bash
hermes insights             # 使用分析（Token 消耗等）
hermes insights --days 30   # 近30天统计
```

### 卸载
```bash
hermes uninstall            # 完全卸载
```

---

## 十、工作区隔离

```bash
hermes -w                   # 隔离 git worktree 模式
hermes --worktree           # 同上，防止并行 Agent 冲突
```

### 使用场景
同时跑多个 Agent 处理不同任务时，`-w` 防止 git 冲突：
```bash
hermes -w -s novel-writing   # Agent A 写小说
hermes -w -s code-review     # Agent B 审代码
```

---

## 十一、辅助功能

### 语音
```bash
# TTS 提供商：edge（免费）、elevenlabs、openai、minimax、mistral
hermes config set tts.provider edge
hermes config set stt.provider local  # 本地语音转文字
```
会话内：`/voice on`（语音对话）、`/voice tts`（始终语音）、`/voice off`

### 界面
```bash
hermes config set display.skin default   # 终端主题
hermes config set display.show_cost true # 显示 Token 费用
hermes config set display.show_reasoning true  # 显示推理过程
hermes config set display.streaming false  # 关闭流式输出
```

### 安全
```bash
hermes config set approvals.mode smart   # 智能审批（推荐）
hermes config set approvals.mode off     # 跳过审批（不推荐）
hermes config set security.redact_secrets true  # 隐藏输出中的密钥
hermes --yolo                            # 临时跳过审批
```

### 调试
```bash
hermes -v                  # 详细输出
hermes -Q                  # 静默模式
hermes --pass-session-id   # 在 system prompt 中包含会话 ID
```

---

## 速查：我最可能用到的

```bash
# 日常
hermes -c                              # 继续上次对话
hermes config set model.default xxx    # 换模型
hermes chat -q "简单问题"               # 快速提问

# 写作
hermes -s novel-writing,novel-writing-workflow  # 带技能启动

# 运维
hermes doctor                          # 体检
hermes gateway status                  # 网关状态
hermes cron list                       # 定时任务

# 学习
hermes skills search <关键词>           # 找技能
hermes skills install <id>             # 装技能
hermes insights --days 7               # 看用量
```
