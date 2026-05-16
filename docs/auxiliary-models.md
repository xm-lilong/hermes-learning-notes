# 辅助模型配置

Hermes 可以在执行后台任务时调用另一个模型，而不是占用主模型。

## 辅助任务列表

| 任务 | 作用 | 频率 |
|------|------|------|
| compression | 对话太长时压缩上下文 | 最频繁 |
| session_search | 搜索历史会话 | 偶尔 |
| title_generation | 自动生成会话标题 | 每次新会话 |
| web_extract | 提取网页内容 | 偶尔 |
| curator | 整理/归档旧会话 | 后台定时 |
| skills_hub | 查询技能市场 | 偶尔 |
| approval | 智能审批命令 | 按需 |
| vision | 图片识别 | 偶尔 |
| mcp | MCP 服务器 | 偶尔 |

## 配置方式

通过 Nous Research 免费模型 `deepseek-v4-flash` 处理所有辅助任务：

```yaml
# config.yaml
auxiliary:
  approval:
    provider: nous
    model: deepseek/deepseek-v4-flash
  compression:
    provider: nous
    model: deepseek/deepseek-v4-flash
  # ... 其余同理
```

## Nous Research 认证

```bash
hermes login --provider nous
```

认证后获得 Agent Key，有效期约 24 小时自动刷新。订阅等级决定速率限制。

## 免费模型选择

通过 Nous 推理 API 可用的完全免费模型：

| 模型 | 类型 | 上下文 |
|------|------|--------|
| `deepseek/deepseek-v4-flash` | 纯文本 | 1M |
| `openrouter/owl-alpha` | 纯文本 | 1M |
| `stepfun/step-3.5-flash` | 纯文本 | 262K |

**注意**：免费模型都是纯文本，不支持图片识别。如需 vision 功能，可用 `google/gemma-3-4b-it`（约 $0.00000012/token，接近免费）。

## 效果

后台任务（压缩、搜索、标题等）全部走免费通道，主模型只用于跟你对话，省钱且高效。
