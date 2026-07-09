# AI 炒股助手 — 安装配置指南

## 前置条件
- Hermes Agent 已安装并运行
- 消息通道已配置（企业微信/Telegram 等）
- Python 3.10+

## 第一步：安装技能

```bash
hermes skill install https://github.com/2024KKK/ai-stock-trading-assistant
```

## 第二步：克隆脚本仓库

```bash
git clone https://github.com/2024KKK/trading-bot.git ~/trading-bot
pip install requests pandas akshare
```

## 第三步：创建 cron 任务

确保 ~/.hermes/config.yaml 中 cron 已启用：
```yaml
cron:
  enabled: true
  schedule_interval: 60
```

创建以下 7 个定时任务：

```bash
# 08:50 盘前提醒
hermes cron create --name "盘前提醒_0850" --schedule "50 8 * * 1-5" --deliver "origin" --toolsets web \
  --prompt "$(cat references/push-0850-prompt.md)"

# 09:00 晨间简报
hermes cron create --name "晨间简报_0900" --schedule "0 9 * * 1-5" --deliver "origin" --toolsets web,file \
  --prompt "$(cat references/push-0900-prompt.md)"

# 10:05 技术面验证（需安装 cn-stock-analysis）
hermes cron create --name "技术面验证_1005" --schedule "5 10 * * 1-5" --deliver "origin" --toolsets web,file,terminal \
  --skills cn-stock-analysis \
  --prompt "$(cat references/push-1005-prompt.md)"

# 12:00 午间暴击
hermes cron create --name "午间暴击_1200" --schedule "0 12 * * 1-5" --deliver "origin" --toolsets web,file \
  --prompt "$(cat references/push-1200-prompt.md)"

# 14:30 尾盘突袭
hermes cron create --name "尾盘突袭_1430" --schedule "30 14 * * 1-5" --deliver "origin" --toolsets web,file,terminal \
  --prompt "$(cat references/push-1430-prompt.md)"

# 15:30 收盘复盘
hermes cron create --name "收盘复盘_1530" --schedule "30 15 * * 1-5" --deliver "origin" --toolsets web,file \
  --prompt "$(cat references/push-1530-prompt.md)"

# 21:00 晚间前瞻
hermes cron create --name "晚间前瞻_2100" --schedule "0 21 * * 1-5" --deliver "origin" --toolsets web,file \
  --prompt "$(cat references/push-2100-prompt.md)"
```

## 第四步：验证

```bash
python scripts/free_data_layer.py --code 600519
hermes cron run --job-id <job_id>
```

## 常见问题
- **推送没收到**：检查 deliver 参数
- **web搜索超时**：09:00 prompt 有内置备用方案推演
- **需要一直开机**：建议部署在云服务器/NAS