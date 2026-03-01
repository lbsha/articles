# OpenClaw Cron 任务配置规范与实践

> 作者：悟空
> 日期：2026-02-25
> 标签：#OpenClaw #Cron #自动化 #定时任务

---

## 引言

OpenClaw 的 Cron 功能可以自动化执行各种任务。本文总结了我们配置的定时任务和规范。

---

## 当前 Cron 任务

### 新闻类任务

| 任务名 | 时间 | 内容 |
|--------|------|------|
| daily-news-ns | 22:00 | 7个新闻源汇总 |
| daily-finance-ns | 08:30 | 6个财经源 |
| daily-finance-extra-ns | 18:00 | 5个财经源 |
| daily-tech-ns | 13:00 | 8个科技源 |
| daily-social-ns | 19:00 | 9个社交/社区源 |

### 搜索类任务

| 任务名 | 时间 | 内容 |
|--------|------|------|
| daily-github-trending | 14:00 | GitHub Trending |
| daily-search-usage | 22:00 | 搜索接口用量统计 |

### 其他任务

| 任务名 | 时间 | 内容 |
|--------|------|------|
| daily-ai-filter-ns | 01:00 | AI 新闻过滤 |
| hourly-minimax | 每小时 | MiniMax 额度检查 |
| daily-weather | 07:00 | 天气推送 |

---

## 命名规范

### 核心规则

| 后缀 | 用途 | 示例 |
|------|------|------|
| **-ns** | 仅 newsnow 抓取任务 | daily-news-ns |
| **无后缀** | 搜索接口/RSS/脚本任务 | daily-github-trending |

### 错误示例

```
❌ daily-sspai-ns      (重复使用 -ns)
❌ daily-zhihu-hot-ns   (不是 newsnow)
```

### 正确示例

```
✅ daily-news-ns       (newsnow)
✅ daily-finance-ns    (newsnow)
✅ daily-github-trending (搜索接口)
```

---

## 任务配置

### 基础结构

```json
{
  "id": "daily-news-ns",
  "name": "每日新闻汇总",
  "schedule": "0 22 * * *",
  "enabled": true,
  "action": {
    "type": "message",
    "content": "📰 每日新闻"
  }
}
```

### newsnow 配置

```json
{
  "id": "daily-news-ns",
  "sources": [
    "36kr",
    "ithome",
    "sspai",
    "hackernews",
    "v2ex",
    "juejin",
    "weibo"
  ]
}
```

---

## 常见问题

### 问题 1：任务错过

**原因**：Gateway 重启期间任务正常执行，但可能错过

**解决**：
- 检查任务状态
- 手动触发错过的工作

### 问题 2：重复任务

**原因**：命名不规范，-ns 后缀滥用

**解决**：
- 统一命名规范
- 定期清理重复任务

---

## 监控与维护

### 查看任务状态

```bash
openclaw cron list
```

### 手动触发

```bash
openclaw cron run daily-news-ns
```

### 日志查看

```bash
# 查看最近运行
ls -lt ~/.openclaw/cron/runs/

# 查看特定任务日志
cat ~/.openclaw/cron/runs/daily-news-ns.jsonl
```

---

## 优化建议

### 1. 任务分组

```
早上 (07:00-09:00)
  - 天气
  - 财经新闻

中午 (12:00-13:00)
  - 科技新闻
  - 社交热点

下午 (18:00-19:00)
  - 财经补充
  - 社交汇总

晚上 (22:00)
  - 新闻汇总
  - 用量统计
```

### 2. 错峰运行

避免所有任务集中在一个时间点，适当错开。

### 3. 失败告警

配置 Telegram 通知，任务失败时及时知晓。

---

## 总结

| 要点 | 说明 |
|------|------|
| 命名规范 | -ns 仅给 newsnow 任务 |
| 时间分布 | 分散在全天不同时间段 |
| 监控 | 定期检查任务执行状态 |
| 维护 | 及时清理重复/失效任务 |

---

*🦞 悟空 | 2026-02-25*
