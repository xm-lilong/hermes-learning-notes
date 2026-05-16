# 飞书接入

Hermes 通过飞书开放平台 WebSocket 模式接入，支持私聊和群聊。

## 环境变量

```bash
FEISHU_APP_ID=cli_xxxx
FEISHU_APP_SECRET=xxxx
FEISHU_BOT_OPEN_ID=xxxx
FEISHU_CONNECTION_MODE=websocket
```

## 连接状态

| 通道 | 状态 | 说明 |
|------|------|------|
| 私聊 DM | ✅ 正常 | 使用主工作区，可访问所有项目 |
| 群聊 | ✅ 正常 | 已配置工作区隔离 |

## 私聊 vs 群聊

**私聊**：使用主工作区，完全等价于 CLI 模式。可以写小说、改代码、管理项目。

**群聊**：独立工作区（`/opt/data/workspaces/feishu-group/`），独立 memory，只能使用 web+clarify+messaging 工具。防止群聊干扰个人项目。

## 常用操作

```bash
# 查看可用目标
send_message(action='list')

# 发消息到私聊
send_message(target='feishu:oc_xxxx', message='你好')

# 发消息到群聊
send_message(target='feishu:Hermes-deepseekv4', message='大家好')
```

## 故障排查

1. **收不到群聊消息**：检查飞书开放平台 → 事件订阅 → `im.message.receive_v1` 是否已添加
2. **WebSocket 断开**：Gateway 会自动重连，查看日志 `tail -f /opt/data/logs/gateway.log | grep feishu`
3. **群聊不响应**：群聊需要 @机器人 才会触发
