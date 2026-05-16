# Hermes Agent 学习笔记

从零到精通 Hermes Agent 的实践记录。基于 Docker 部署、DeepSeek V4 Pro 主模型、Nous Research 辅助模型的真实环境。

## 目录

| 文档 | 说明 |
|------|------|
| [Cron 定时任务](docs/cron-jobs.md) | 踩坑全记录：权限、调度、模型兼容性 |
| [代理配置](docs/proxy-setup.md) | 容器内 mihomo + clash 订阅，隔离宿主机 |
| [辅助模型](docs/auxiliary-models.md) | Nous Research 免费模型分担后台任务 |
| [飞书接入](docs/feishu-integration.md) | WebSocket 私聊/群聊，工作区隔离 |
| [微信接入](docs/weixin-integration.md) | iLink Bot 配置、限频处理 |
| [Cron 故障排查](docs/cron-debugging.md) | 完整诊断树：6 类失败及修复 |
| [CLI 命令完全手册](docs/cli-commands.md) | 所有命令按场景分类，速查表 |

## 环境概览

```
容器: Docker (hermes-agent)
系统: Debian 13 (trixie) x86_64
主模型: deepseek-v4-pro (DeepSeek API)
辅助模型: deepseek-v4-flash (Nous Research, 免费)
代理: mihomo v1.19.4 + clash 订阅 (端口 7890)
消息平台: 飞书 ✅ | 微信 ⚠️ 限频
GitHub: xm-lilong
```

## 核心教训

1. **Cron 别用 DeepSeek** — 不支持 function calling，100% 幻觉
2. **用 `docker exec -u hermes`** — root 进入会导致权限地狱
3. **Cron 间隔用 cron 表达式** — `*/15 * * * *` 比 `every 15m` 可靠
4. **辅助模型省大钱** — 后台任务全部走免费通道
5. **代理跑容器内** — 不碰宿主机 Tailscale/1Panel
