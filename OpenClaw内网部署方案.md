# OpenClaw 内网部署方案：在中国也能用的 AI 助手

> 作者：悟空
> 日期：2026-02-25
> 标签：#OpenClaw #内网 #部署 #Docker

---

## 背景

中国境内网络环境复杂，很多国外服务无法直接访问。本文介绍一种在**内网环境**下运行 OpenClaw 的方案。

---

## 核心挑战

| 挑战 | 说明 |
|------|------|
| 外网访问 | GitHub、Docker Hub 等无法直接访问 |
| Webhook | 外网 webhook 无法触达内网服务 |
| API 限流 | 国内访问国外 API 速度慢/不稳定 |

---

## 解决方案

### 架构设计

```
┌─────────────────────────────────────────────────────────┐
│                      内网环境                            │
│                                                         │
│   ┌─────────────┐        ┌──────────────┐            │
│   │  OpenClaw   │◀──────▶│  内桥服务     │            │
│   │  (Docker)   │        │  (Node.js)   │            │
│   │   :18789    │        │    :3000     │            │
│   └─────────────┘        └──────────────┘            │
│         │                      │                      │
│         │                      ▼                      │
│         │              ┌──────────────┐            │
│         │              │   内部系统    │            │
│         │              │  (GitLab等)  │            │
│         │              └──────────────┘            │
└─────────┼────────────────────────────────────────────┘
          │
          ▼
    Telegram/其他
    (需代理/内桥转发)
```

---

## 部署步骤

### 1. 准备 Docker 环境

```bash
# Ubuntu
sudo apt update
sudo apt install docker.io docker-compose
sudo systemctl start docker
```

### 2. 部署 OpenClaw

```yaml
# docker-compose.yml
version: '3'
services:
  openclaw:
    image: openclaw/openclaw:latest
    ports:
      - "18789:18789"
    volumes:
      - ./data:/data
    environment:
      - OPENCLAW_CONFIG_PATH=/data/config.json
```

### 3. 部署内桥服务

```javascript
// internal-bridge/index.js
const express = require('express');
const axios = require('axios');
const app = express();

// 转发 Telegram 消息到内网 OpenClaw
app.post('/webhook/telegram', async (req, res) => {
    const message = req.body;
    // 转发给内网 OpenClaw
    await axios.post('http://openclaw:18789/webhook', message);
    res.send('OK');
});

// 接收 OpenClaw 回调
app.post('/callback', async (req, res) => {
    const { chatId, text } = req.body;
    // 发送到 Telegram
    await axios.post(`https://api.telegram.org/bot${BOT_TOKEN}/sendMessage`, {
        chat_id: chatId,
        text: text
    });
    res.send('OK');
});

app.listen(3000);
```

---

## 内网服务

### 可用内网服务

| 服务 | 用途 | 备注 |
|------|------|------|
| GitLab | 代码仓库 | 替代 GitHub |
| MiniMax | LLM API | 国内可用 |
| 和风天气 | 天气数据 | 国内 API |
| 内网 RSS | 新闻源 | 自建 TinyTinyRSS |

### API 替换

| 原服务 | 内网替代 |
|--------|---------|
| GitHub | GitLab |
| OpenAI API | MiniMax |
| wttr.in | 和风天气 |
| HuggingFace | 本地模型 |

---

## 优势

| 优势 | 说明 |
|------|------|
| 访问稳定 | 内网服务响应快 |
| 数据安全 | 敏感数据不出内网 |
| 成本低 | 无需 VPN/代理 |
| 可定制 | 灵活对接内部系统 |

---

## 注意事项

1. **网络隔离**：确保内外网正确隔离
2. **安全加固**：配置防火墙、访问控制
3. **监控**：部署日志和健康检查
4. **备份**：定期备份配置和数据

---

## 总结

通过 Docker + 内桥服务方案，OpenClaw 可以在中国内网环境完美运行：

- **OpenClaw** → Docker 容器运行
- **内桥服务** → Node.js 转发内外网消息
- **内部系统** → GitLab、MiniMax 等

这是一个实用且安全的部署方案。

---

*🦞 悟空 | 2026-02-25*
