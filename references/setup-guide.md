# AI 炒股助手 — 安装配置指南

## 前置条件
- Hermes Agent 已安装并运行
- 消息通道已配置（企业微信/Telegram 等）
- Python 3.10+

## 第一步：安装技能

```bash
hermes skill install https://github.com/2024KKK/ai-stock-trading-assistant
```

## 第二步：安装 Agent-Reach（提供稳定网页读取能力）

```bash
pip install https://github.com/Panniantong/agent-reach/archive/main.zip
```

安装后即可使用 Jina Reader 读取财经网站：
```bash
# 读东方财富网头条（09:00推送用）
curl -s --max-time 8 "https://r.jina.ai/https://finance.eastmoney.com/" | head -30

# 读美股行情（21:00推送用）
curl -s --max-time 8 "https://r.jina.ai/https://finance.yahoo.com/" | head -30
```

## 第三步：克隆脚本仓库

```bash
git clone https://github.com/2024KKK/trading-bot.git ~/trading-bot
pip install requests pandas akshare
```

## 第四步：配置 cron 任务

确保 ~/.hermes/config.yaml 中 cron 已启用：
```yaml
cron:
  enabled: true
  schedule_interval: 60
```

创建以下 7 个定时任务（注意 09:00 和 21:00 需要 terminal 工具集跑 curl 和 Python 脚本）：

```bash
# 08:50 盘前提醒
hermes cron create --name "盘前提醒_0850" --schedule "50 8 * * 1-5" --deliver "origin" --toolsets web \
  --prompt "$(cat references/push-0850-prompt.md)"

# 09:00 晨间简报（Jina Reader + free_data_layer）
hermes cron create --name "晨间简报_0900" --schedule "0 9 * * 1-5" --deliver "origin" --toolsets file,terminal \
  --prompt "$(cat references/push-0900-prompt.md)"

# 10:05 技术面验证（需安装 cn-stock-analysis）
hermes cron create --name "技术面验证_1005" --schedule "5 10 * * 1-5" --deliver "origin" --toolsets file,terminal \
  --skills cn-stock-analysis \
  --prompt "$(cat references/push-1005-prompt.md)"

# 12:00 午间暴击
hermes cron create --name "午间暴击_1200" --schedule "0 12 * * 1-5" --deliver "origin" --toolsets web,file \
  --prompt "$(cat references/push-1200-prompt.md)"

# 14:30 尾盘突袭
hermes cron create --name "尾盘突袭_1430" --schedule "30 14 * * 1-5" --deliver "origin" --toolsets file,terminal \
  --prompt "$(cat references/push-1430-prompt.md)"

# 15:30 收盘复盘
hermes cron create --name "收盘复盘_1530" --schedule "30 15 * * 1-5" --deliver "origin" --toolsets file,terminal \
  --prompt "$(cat references/push-1530-prompt.md)"

# 21:00 晚间前瞻（Jina Reader + free_data_layer）
hermes cron create --name "晚间前瞻_2100" --schedule "0 21 * * 1-5" --deliver "origin" --toolsets file,terminal \
  --prompt "$(cat references/push-2100-prompt.md)"
```

## 第五步：验证

```bash
# 测试实时行情
python scripts/free_data_layer.py --code 600519

# 测试 Jina Reader（返回干净文本）
curl -s --max-time 6 "https://r.jina.ai/https://finance.eastmoney.com/" | head -10

# 手动触发一次09:00推送测试
hermes cron run --job-id <job_id>
```

## 可选增强

| 功能 | 命令 | 说明 |
|------|------|------|
| cn-stock-analysis | `hermes skill install https://github.com/shaohk/TradingAgents-CN-SKILL` | 15Agent深度分析单只股票 |
| Tushare数据 | 设置 `TUSHARE_TOKEN` 环境变量 | 历史日线数据 |
| RSS新闻源 | 在prompt中用 feedparser 订阅 | 每日自动推送新闻摘要 |

## 常见问题

- **推送没收到**：检查 deliver 参数。如果是 "origin" 确保 WECOM_HOME_CHANNEL 已配置。
- **curl: command not found**：安装 curl（Windows用户安装 Git for Windows 自带 curl）
- **需要一直开机**：建议部署在云服务器/NAS