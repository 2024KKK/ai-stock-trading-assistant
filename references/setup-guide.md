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

## 第三步：克隆脚本仓库

```bash
git clone https://github.com/2024KKK/trading-bot.git ~/trading-bot
pip install requests pandas akshare
```

## 第四步：创建 cron 任务

确保 ~/.hermes/config.yaml 中 cron 已启用：
```yaml
cron:
  enabled: true
  schedule_interval: 60
```

创建以下 8 个 cron 任务（严格按照此流程）：

```bash
# 1. 08:50 盘前提醒 — 持仓卖出纪律
hermes cron create --name "盘前提醒_0850" --schedule "50 8 * * 1-5" --deliver "origin" --toolsets web \
  --prompt "$(cat references/push-0850-prompt.md)"

# 2. 09:00 晨间简报 — 今日情绪+方向
hermes cron create --name "晨间简报_0900" --schedule "0 9 * * 1-5" --deliver "origin" --toolsets file,terminal \
  --prompt "$(cat references/push-0900-prompt.md)"

# 3. 10:05 技术面验证（需 cn-stock-analysis 技能）
hermes cron create --name "技术面_1005" --schedule "5 10 * * 1-5" --deliver "origin" --toolsets file,terminal \
  --skills cn-stock-analysis \
  --prompt "$(cat references/push-1005-prompt.md)"

# 4. 12:00 午间暴击 — 半日盘面更新
hermes cron create --name "午间_1200" --schedule "0 12 * * 1-5" --deliver "origin" --toolsets web,file \
  --prompt "$(cat references/push-1200-prompt.md)"

# 5. 14:30 尾盘执行 — 一夜持股法选股+买入
hermes cron create --name "尾盘_1430" --schedule "30 14 * * 1-5" --deliver "origin" --toolsets file,terminal,web \
  --prompt "$(cat references/push-1430-prompt.md)" \
  --workdir ~/trading-bot

# 6. 15:30 收盘复盘
hermes cron create --name "收盘_1530" --schedule "30 15 * * 1-5" --deliver "origin" --toolsets file,terminal \
  --prompt "$(cat references/push-1530-prompt.md)"

# 7. 21:00 晚间前瞻 — 明日可操作性检查
hermes cron create --name "晚间_2100" --schedule "0 21 * * 1-5" --deliver "origin" --toolsets file,terminal,web \
  --prompt "$(cat references/push-2100-prompt.md)"

# 8. 18:30 数据同步（可选，需 Tushare Token）
hermes cron create --name "数据同步_1830" --schedule "30 18 * * 1-5" --deliver "local" --toolsets terminal,file \
  --prompt "执行 python scripts/tushare_sync.py" \
  --workdir ~/trading-bot
```

## 第五步：验证

```bash
# 测试实时行情
python scripts/free_data_layer.py --code 600519

# 测试 Jina Reader
curl -s --max-time 6 "https://r.jina.ai/https://finance.eastmoney.com/" | head -10
```

## 核心理念

**一夜持股法** — 14:50-14:55买入，次日9:30-9:40卖出，不扛套

## 常见问题

- **推送没收到**：检查 deliver 参数
- **curl找不到**：Windows用户安装Git for Windows自带curl
- **需要一直开机**：建议部署云服务器/NAS