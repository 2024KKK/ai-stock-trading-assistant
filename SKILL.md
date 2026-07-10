---
name: ai-stock-trading-assistant
description: 完整A股AI炒股助手系统 — 告别选股哲学 + 看A做B策略 + 固定龙头库 + 多Agent技术分析 + Agent-Reach网页读取。每日自动推送，覆盖盘前/早盘/午盘/尾盘/收盘/晚间。
author: k@11h5.com
tags: [a-share, trading, stock, cron, push, mainboard, ai-assistant, agent-reach]
---

# AI 炒股助手 — 完整交易员系统

## 核心理念：告别选股 + 看A做B

### 告别选股
不需要每天从5000只A股里大海捞针。**固定龙头库**覆盖18个核心板块、31只标的，每个板块只盯龙头和龙二/中军。

### 看A做B
如果龙头A涨停/一字板买不进 → 立刻查同板块B标的（龙二/中军）→ 估值更低、位置更好、性价比更高。

### 交易纪律
- **一夜持股法**：14:30买入 → 次日9:30-9:40卖出，除非强势涨停
- **分仓平铺**：每天1-3只，每只轻仓5-8%，总仓不超过10%
- **去弱留强**：次日开盘谁强留谁，谁弱卖谁
- **早盘不买**：只尾盘14:30后动手

### 范围限制
- **只炒沪深主板**：60/00开头
- **排除**：688（科创板）、300（创业板）、北交所、港股
- **只买固定龙头库中的标的**（除非新闻出现全新的强催化板块）

---

## 技术栈

| 组件 | 用途 |
|------|------|
| [Hermes Agent](https://hermes-agent.nousresearch.com) | AI Agent框架，运行cron定时推送 |
| [Agent-Reach](https://github.com/Panniantong/Agent-Reach) | 互联网能力层：Jina Reader读取财经新闻 |
| [cn-stock-analysis](https://github.com/shaohk/TradingAgents-CN-SKILL) | 15Agent多维度深度分析（可选） |
| free_data_layer.py | 新浪/腾讯实时行情 |
| mainboard_leaders.py | 固定龙头库（18板块31只） |
| tushare_sync.py | 历史日线数据同步（可选） |

---

## 每日推送时间表

| 时间 | 名称 | 内容 | 数据源 |
|------|------|------|--------|
| 08:50 | 盘前提醒 | 简短策略提醒 | 纯文本 |
| 09:00 | 晨间简报 | 新闻→情绪罗盘→荐股 | Jina Reader + free_data_layer |
| 10:05 | 技术面验证 | 打分表+技术指标 | free_data_layer + delegate_task |
| 12:00 | 午间暴击 | 半日盘面+新催化 | Jina Reader |
| 14:30 | 尾盘突袭 | 最终入场计划 | free_data_layer |
| 15:30 | 收盘复盘 | 荐股涨跌回顾 | free_data_layer |
| 21:00 | 晚间前瞻 | 外盘+明日盯盘 | Jina Reader + free_data_layer |

---

## 数据流

```
Jina Reader (r.jina.ai) ──── 读财经新闻 → 匹配龙头库 → 荐股
free_data_layer.py (新浪) ── 实时行情: 价/量/涨跌幅/换手率
delegate_task (cn-stock-analysis) ─ 技术分析子代理 → 评分/MA/MACD/RSI
```

---

## 安装

```bash
# 1. 安装技能
hermes skill install https://github.com/2024KKK/ai-stock-trading-assistant

# 2. 安装 Agent-Reach
pip install https://github.com/Panniantong/agent-reach/archive/main.zip

# 3. 克隆脚本
git clone https://github.com/2024KKK/trading-bot.git ~/trading-bot
pip install requests pandas akshare

# 4. 配置 cron（见 references/setup-guide.md）
```

---

## License

MIT
