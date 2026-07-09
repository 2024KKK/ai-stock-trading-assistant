# cn-stock-analysis 多Agent分析集成

cn-stock-analysis 是15角色多Agent并行分析框架，用于A股深度分析。

## 集成方式

### 方式1：定时推送中调用
10:05推送使用 delegate_task 启动技术分析子代理。

### 方式2：对话命令
`分析 600584` → 全流程多Agent分析

### 方式3：全流程15Agent

Phase 1: 7分析师（技术/新闻/市场/基本面/情绪/中国/社交媒体）
Phase 2: 多空辩论
Phase 3: 风险经理+研究经理+交易员决策

## 安装

```bash
hermes skill install https://github.com/shaohk/TradingAgents-CN-SKILL
```

## 数据流

free_data_layer.py → 填入delegate context → subagent技术分析 → 评分输出