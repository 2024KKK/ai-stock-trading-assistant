# AI 炒股助手 — Hermes Agent Skill

> 告别选股哲学 + 看A做B策略 + 固定龙头库 + 多Agent技术分析 + Agent-Reach 网页读取

一个为 [Hermes Agent](https://hermes-agent.nousresearch.com) 设计的完整A股AI炒股助手系统。
每天自动推送5-7次操盘建议，覆盖盘前/早盘/午盘/尾盘/收盘/晚间。

---

## 核心理念

**告别选股** — 固定龙头库覆盖18个核心板块、31只标的

**看A做B** — 龙头A涨停买不进就找同板块B标的

**一夜持股法** — 14:30买入 → 次日9:30-9:40卖出

---

## 技术栈

| 组件 | 用途 |
|------|------|
| [Hermes Agent](https://hermes-agent.nousresearch.com) | AI Agent 框架，运行 cron 定时推送 |
| [Agent-Reach](https://github.com/Panniantong/Agent-Reach) | 互联网能力层：Jina Reader 读取财经新闻，feedparser RSS订阅 |
| [cn-stock-analysis](https://github.com/shaohk/TradingAgents-CN-SKILL) | 15Agent多维度深度分析（可选增强） |
| free_data_layer.py | 新浪/腾讯实时行情（股价、涨跌幅、成交量） |
| mainboard_leaders.py | 固定龙头库（18板块31只） |
| Tushare DB | 历史日线数据（可选） |

---

## 快速安装

```bash
# 1. 安装技能
hermes skill install https://github.com/2024KKK/ai-stock-trading-assistant

# 2. 安装 Agent-Reach（提供稳定网页读取能力）
pip install https://github.com/Panniantong/agent-reach/archive/main.zip

# 3. 克隆配套脚本
git clone https://github.com/2024KKK/trading-bot.git ~/trading-bot
pip install requests pandas akshare

# 4. 按 references/setup-guide.md 创建 cron 任务
```

---

## 每日推送时间表

| 时间 | 名称 | 内容 | 数据来源 |
|------|------|------|---------|
| 08:50 | 盘前提醒 | 简短策略提醒 | — |
| 09:00 | 晨间简报 | 新闻→情绪→荐股 | Jina Reader + free_data_layer |
| 10:05 | 技术面验证 | 评分表+技术指标 | free_data_layer + delegate_task |
| 12:00 | 午间暴击 | 半日盘面+新催化 | Jina Reader / web |
| 14:30 | 尾盘突袭 | 最终入场计划 | free_data_layer |
| 15:30 | 收盘复盘 | 荐股涨跌回顾 | free_data_layer |
| 21:00 | 晚间前瞻 | 外盘+明日清单 | Jina Reader + free_data_layer |

---

## 聊天气泡（对话命令）

```
分析 600584    → cn-stock-analysis 深度分析
开早盘         → 执行晨间简报
荐股           → 基于当前信息推荐
```

---

## License

MIT