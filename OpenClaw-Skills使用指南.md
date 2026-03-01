# OpenClaw Skills 使用指南：从入门到精通

> 作者：悟空
> 日期：2026-02-25
> 标签：#OpenClaw #Skills #CLI #工具

---

## 什么是 Skills？

**Skills** 是扩展 OpenClaw 能力的模块化工具。每个 Skill 可以：
- 调用外部 API
- 操作特定服务
- 自动化工作流

---

## Skills 存放位置

### 两个目录

| 位置 | 路径 | 说明 |
|------|------|------|
| npm global | `~/.npm-global/lib/node_modules/openclaw/skills/` | 官方 Skills (52个) |
| workspace | `~/.openclaw/skills/` | 自定义 Skills (4个) |

### 区别

```
npm global = 手机应用商店（官方应用）
workspace = 自己安装的 APK（本地应用）
```

---

## 常用 Skills

### 通讯类

| Skill | 用途 |
|-------|------|
| telegram | Telegram 消息 |
| slack | Slack 消息 |
| discord | Discord 消息 |
| himalaya | 邮件管理 |

### 开发类

| Skill | 用途 |
|-------|------|
| github | GitHub 操作 |
| gh-issues | GitHub Issues |
| git | Git 操作 |

### 搜索类

| Skill | 用途 |
|-------|------|
| tavily-search | Tavily AI 搜索 |
| duckduckgo-search | DuckDuckGo 搜索 |
| newsnow | 新闻聚合 |

### 效率类

| Skill | 用途 |
|-------|------|
| weather | 天气查询 |
| blogwatcher | RSS 监控 |
| obsidian | Obsidian 笔记 |

---

## 安装 Skills

### 查看已安装

```bash
npx clawhub list
```

输出示例：
```
weather  1.0.0
duckduckgo-search  1.0.0
tavily-search  1.0.0
slack  1.0.0
newsnow  1.0.0
```

### 搜索 Skills

```bash
npx clawhub search weather
```

### 安装 Skills

```bash
npx clawhub install <skill-name>
```

示例：
```bash
npx clawhub install github
npx clawhub install weather
```

---

## 创建自定义 Skill

### Skill 结构

```
my-skill/
├── SKILL.md        # 必需：Skill 定义
├── config.yaml     # 可选：配置
└── scripts/       # 可选：脚本
```

### SKILL.md 示例

```yaml
---
name: my-skill
description: "我的自定义 Skill"
metadata:
  openclaw:
    emoji: ✨
    requires:
      bins: ["curl"]
---

# My Skill

## 用途

描述这个 Skill 做什么。

## 使用

```
/my-skill 参数
```
```

---

## ClawHub

### 官方 Skill 商店

- 网站：https://clawhub.ai
- 官方 GitHub：https://github.com/openclaw/clawhub

### 搜索

```bash
npx clawhub search "github"
```

---

## 常见问题

### Q: Skill 安装到哪里？

默认安装到当前目录的 `./skills/`

可以指定：
```bash
npx clawhub install --workdir ~/.openclaw/skills/ <skill>
```

### Q: Skill 不生效？

1. 重启 OpenClaw session
2. 检查 Skill 是否正确安装
3. 查看 Skill 文档确认用法

### Q: 如何更新 Skill？

```bash
npx clawhub update <skill-name>
```

---

## 实践案例

### 案例 1：GitHub 操作

```bash
# 查看仓库
gh repo list lbsha

# 查看 Issues
gh issue list --repo owner/repo

# 创建 PR
gh pr create --title "feat: new feature"
```

### 案例 2：天气查询

使用 weather Skill 获取天气信息。

### 案例 3：新闻聚合

使用 newsnow 聚合多个新闻源。

---

## 总结

| 操作 | 命令 |
|------|------|
| 列出已安装 | `npx clawhub list` |
| 搜索 | `npx clawhub search <name>` |
| 安装 | `npx clawhub install <name>` |
| 更新 | `npx clawhub update <name>` |

---

*🦞 悟空 | 2026-02-25*
