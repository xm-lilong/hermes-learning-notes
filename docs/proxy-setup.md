# 代理配置

在 Docker 容器内运行 mihomo + clash 订阅，完全不碰宿主机网络。

## 架构

容器内：`你的程序 → localhost:7890 (mihomo) → clash 订阅节点 → 外网`
宿主机：Tailscale / 1Panel 完全不受影响

## 组件

| 组件 | 路径 |
|------|------|
| mihomo | `/opt/data/bin/mihomo` (v1.19.4) |
| 配置 | `/opt/data/mihomo/config.yaml` |
| 启动 | `/opt/data/scripts/proxy-start.sh` |
| 停止 | `/opt/data/scripts/proxy-stop.sh` |
| 端口 | `127.0.0.1:7890` |

## 使用

```bash
/opt/data/scripts/proxy-start.sh   # 启动
git push                            # 自动走代理
/opt/data/scripts/proxy-stop.sh    # 停止
```

Git 已全局配置 `http.proxy` / `https.proxy` → `127.0.0.1:7890`。
代理不启动时 git 秒拒（Connection Refused），不会超时卡住。

## 更新订阅

mihomo 自动更新。手动更新：
```bash
curl -sL "订阅URL&flag=clash" -o /opt/data/mihomo/config.yaml
# 重启 mihomo
```

## 为什么不用宿主机代理

宿主机跑着 Tailscale、1Panel 等，开全局代理会干扰。容器内完全隔离。
