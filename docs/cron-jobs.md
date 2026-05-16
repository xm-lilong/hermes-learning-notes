# Cron 定时任务

Hermes 的 cron 系统可以让 Agent 定时自主执行任务，完成后推送到指定平台。

## 核心概念

```
你设好任务 → 定时触发 → Agent 独立会话 → 通知你结果
```

Cron 会话与 CLI 完全独立——没有你的对话记忆，不能提问。

## 创建方式

**cronjob 工具（推荐）：**
```
cronjob(action='create', schedule='*/30 * * * *', prompt='...', deliver='feishu')
```

**CLI 命令：**
```bash
hermes cron create '*/30 * * * *' -q '你的任务'
```

## 关键参数

| 参数 | 说明 | 示例 |
|------|------|------|
| `schedule` | 触发频率 | `'*/30 * * * *'`、`'every 1h'`、`'0 9 * * *'` |
| `prompt` | 自包含任务指令 | Agent 没有上下文记忆 |
| `repeat` | 执行次数 | `10` 次后自动停止 |
| `deliver` | 结果推送 | `'weixin'`、`'feishu'`、`'local'` |
| `enabled_toolsets` | 允许的工具 | `['file','terminal','skills']` — 不给会幻觉 |
| `skills` | 预加载技能 | `['novel-writing', 'novel-writing-workflow']` |
| `workdir` | 工作目录 | 项目路径 |
| `no_agent` | 纯脚本模式 | 跳过 LLM，直接跑脚本 |

## 六大常见故障

### 1. 权限地狱（最频繁）
**症状**：任务永远 `state: scheduled`，从不执行
**原因**：以 root 进容器创建的任务，`jobs.json` 属 root:root 600，hermes 用户读不了
**修复**：
```python
import os; os.chmod('/opt/data/cron/jobs.json', 0o666)
```
**根治**：`docker exec -u hermes` 进入容器

### 2. DeepSeek V4 Pro 不支持 Function Calling（硬伤）
**症状**：任务执行了但输出是幻觉——Agent 声称写了文件、给了假 commit hash，实际什么都没做
**原因**：DeepSeek 在 cron 会话中不调用工具，只能纯文本输出
**验证**：`git show <假hash>` → "unknown revision"
**结论**：涉及写文件、git 操作的 cron 任务不能用 DeepSeek。只读监控（ls/stat/wc）可以。

### 3. 没给工具导致幻觉
**症状**：同上
**原因**：没设 `enabled_toolsets`，Agent 拿不到工具
**修复**：创建时必加 `enabled_toolsets: ['file', 'terminal', 'skills']`

### 4. Grace Window 快进
**症状**：`next_run_at` 已过期但任务不触发
**原因**：短间隔（`every 2m`）grace 窗口太窄，扫描错过就跳到下轮
**修复**：用 cron 表达式代替 interval
```
❌ schedule='every 2m'   → grace=120s, 几乎不可能命中
❌ schedule='every 10m'  → grace=300s, 紧张
✅ schedule='*/15 * * * *' → grace=450s, 可靠
✅ schedule='*/30 * * * *' → grace=900s, 很安全
```

### 5. 微信限频 → 网关死锁
**症状**：日志停在 `rate limited`，所有 cron 停摆
**原因**：微信限频后网关内部 ticker 线程卡死
**修复**：重启网关

### 6. Skill 文件权限
**症状**：Agent 声称完成了工作但实际什么都没做
**原因**：skill 文件属 root:root 600，cron agent（hermes 用户）读不了
**修复**：
```bash
chown -R hermes:hermes /opt/data/skills/
find /opt/data/skills -type f -exec chmod 644 {} \;
```

## 什么适合/不适合 Cron

| ✅ 适合 | ❌ 不适合 |
|---------|----------|
| 只读监控（ls, stat, wc） | 写文件、git 操作（DeepSeek 下） |
| `no_agent` 纯脚本 | 需要判断和决策的复杂任务 |
| 简单通知 | 小说写作 |
| 其他支持 function calling 的模型 | 需要跟用户交互的任务 |
