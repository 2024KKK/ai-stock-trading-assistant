# Agent-Reach 集成说明

[Agent-Reach](https://github.com/Panniantong/Agent-Reach) 是一个 AI Agent 互联网能力层，为 AI 炒股助手提供稳定的网页读取能力。

## 核心能力

### Jina Reader — 读任意网页返回干净文本

```bash
curl -s "https://r.jina.ai/URL"
```

无需 API Key，免费使用。返回干净的 Markdown 文本（无 HTML 标签、无广告）。

### 在推送中如何使用

**09:00 晨间简报** — 读东方财富网头条：
```bash
curl -s --max-time 8 "https://r.jina.ai/https://finance.eastmoney.com/" | head -40
```
→ 返回今日热点新闻标题 → 匹配固定龙头库 → 荐股

**12:00 午间暴击** — 读午间财经：
```bash
curl -s --max-time 8 "https://r.jina.ai/https://finance.eastmoney.com/" | head -20
```

**21:00 晚间前瞻** — 读美股行情：
```bash
curl -s --max-time 8 "https://r.jina.ai/https://finance.yahoo.com/" | head -40
```
→ 提取道指/纳指/标普涨跌 → 推演明日A股情绪

### feedparser — RSS 订阅（高级）

可订阅财经 RSS 源，每日自动推送：
```bash
python3 -c "
import feedparser
f = feedparser.parse('https://rss.url')
for e in f.entries[:5]:
    print(f'{e.title}')
"
```

## 安装

```bash
pip install https://github.com/Panniantong/agent-reach/archive/main.zip
```

## 验证

```bash
curl -s --max-time 6 "https://r.jina.ai/https://www.baidu.com" | head -5
```
返回干净的 Markdown 即安装成功。

## 为什么比 web 搜索好

| 对比项 | Hermes web搜索 | Jina Reader |
|--------|---------------|-------------|
| 速度 | 2-30秒（含 Cloudflare 路由） | 1-3秒 |
| 稳定性 | 经常 524 超时 | 几乎不超时 |
| 输出质量 | 原始 HTML | 干净 Markdown |
| API Key | 不需要 | 不需要 |
| 频率限制 | 有 | 宽松 |