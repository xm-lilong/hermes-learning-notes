# 微信接入

Hermes 原生支持微信 iLink Bot 协议，通过扫码登录。

## 环境变量

```bash
WEIXIN_ACCOUNT_ID=b727e52b1cbf@im.bot
WEIXIN_TOKEN=xxxx
WEIXIN_BASE_URL=https://ilinkai.weixin.qq.com
WEIXIN_HOME_CHANNEL=o9cq80xHNICSeRQ5Nth-h1-qHzVk@im.wechat
```

## 当前状态

⚠️ **限频不可用** — 微信 iLink 平台对消息频率有严格限制，频繁发送会触发 rate limit

## 接入流程

```bash
hermes gateway setup   # 选择微信 → 扫码登录
```

## 限制

- iLink Bot 身份（`@im.bot`）通常无法加入普通微信群
- 群聊策略默认禁用（`WEIXIN_GROUP_POLICY=disabled`）
- 消息频率有硬限制

## 替代方案

飞书私聊目前是更稳定的消息通道。微信可保留用于偶尔的通知推送（低频）。
