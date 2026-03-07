---
title: OpenClaw 小龙虾 AI 助手完全指南：5 分钟部署你的私人 AI（2026）
published: true
description: OpenClaw 小龙虾 + Crazyrouter，一个 Key 调用 627+ 模型，5 分钟部署 Telegram AI 助手，月费仅 $13 起。
tags: 'ai, chinese, telegram, tutorial'
canonical_url: null
id: 3323994
date: '2026-03-07T18:33:13Z'
---


> **核心结论：** OpenClaw（小龙虾）是一款开源 AI 助手框架，支持 Telegram、Discord、微信等 20+ 平台。搭配 Crazyrouter 作为 AI 后端，一个 API Key 即可调用 627+ 模型（GPT-5、Claude、Gemini、DeepSeek），价格仅为官方的 45%，5 分钟内完成部署。

---

## 什么是 OpenClaw（小龙虾）？

OpenClaw，中文社区昵称"小龙虾"，是一个**开源的个人 AI 助手框架**。它能将任何聊天平台变成 AI 驱动的智能助手。

**与 ChatGPT 的区别：**

| 对比项 | ChatGPT Plus | OpenClaw 小龙虾 + Crazyrouter |
|--------|-------------|-------------------------------|
| 月费 | $20 固定 | $13-30（按量付费） |
| 模型 | 仅 GPT | 627+ 模型（GPT/Claude/Gemini/DeepSeek...） |
| 平台 | 网页/App | 20+ 平台（Telegram/Discord/微信/钉钉...） |
| 记忆 | 有限 | 持久化跨会话记忆 |
| 数据 | 存在 OpenAI | 存在你自己的服务器 |
| 扩展 | 无 | 技能系统（搜索/代码/浏览器/下载...） |
| 模型切换 | 手动选 | 对话中随时切换 |

**一句话总结：** ChatGPT 是租房，OpenClaw 是买房——自己的数据、自己的规则、自己的 AI。

---

## 为什么用 Crazyrouter 做后端？

OpenClaw 需要一个 AI 模型 API 来提供智能。你可以直接用 OpenAI、Anthropic 的官方 API，但用 Crazyrouter 有三大优势：

### 1. 一个 Key 调 627+ 模型

不用分别注册 OpenAI、Anthropic、Google、DeepSeek……一个 Crazyrouter Key 全搞定。

### 2. 价格约为官方 55%

| 模型 | 官方价格（输入/百万 token） | Crazyrouter 价格 | 节省 |
|------|------------------------|-----------------|------|
| GPT-5.2 | $10.00 | $4.50 | 55% |
| Claude Opus 4.6 | $15.00 | $6.75 | 55% |
| Gemini 3 Pro | $3.50 | $1.58 | 55% |
| DeepSeek R1 | ¥4.00 | ¥0.40 | 90% |
| 通义千问 Qwen3 | ¥4.00 | ¥0.40 | 90% |

国产模型更是低至 **1 折**。

### 3. 额度永不过期

OpenAI 官方额度 3 个月到期，Crazyrouter 永不过期。充 5 块钱能用几个月。

---

## 部署方式一：一行命令（最快）

```bash
curl -fsSL https://raw.githubusercontent.com/xujfcn/crazyrouter-openclaw/main/install.sh | bash
```

脚本自动完成：
1. 检测系统（Linux/macOS，x64/arm64）
2. 安装 Node.js 22+
3. 安装 OpenClaw
4. 收集你的 Crazyrouter API Key
5. 预配置 15+ 热门模型
6. 配置开机自启（systemd/launchd）
7. 启动网关
8. 引导 Telegram Bot 配对

### 需要准备的

**Crazyrouter API Key：**
1. 打开 [crazyrouter.com](https://crazyrouter.com?utm_source=csdn&utm_medium=tutorial&utm_campaign=dev_community)
2. 注册（免费，送 $0.20 体验额度）
3. 复制 API Key（`sk-` 开头）

**Telegram Bot Token：**
1. Telegram 搜 @BotFather
2. 发送 `/newbot` → 设置名称
3. 复制 Token

---

## 部署方式二：Docker 部署

```bash
git clone https://github.com/xujfcn/openclaw-deploy.git
cd openclaw-deploy
chmod +x deploy.sh
sudo ./deploy.sh
```

选择 Docker 模式，输入两个 Key，等待 2 分钟。

---

## 部署方式三：手动安装

```bash
# 安装 Node.js
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
source ~/.bashrc
nvm install 22

# 安装 OpenClaw
npm install -g openclaw

# 配置
openclaw configure
```

配置时填写：
- Base URL：`https://crazyrouter.com/v1`
- API Key：你的 Crazyrouter Key
- 默认模型：`claude-opus-4-6`

---

## Telegram Bot 配对

```bash
openclaw configure --section telegram
# 输入 Bot Token
```

1. Telegram 给 Bot 发 `/start`
2. Bot 回复配对码
3. 终端输入配对码，或运行：`openclaw pairing approve telegram <配对码>`
4. 开始聊天！

---

## 模型选择指南

Crazyrouter 后端让你随时切换模型，以下是不同场景的推荐：

| 场景 | 推荐模型 | Crazyrouter 价格 | 说明 |
|------|---------|-----------------|------|
| 日常助手 | Claude Opus 4.6 | $6.75/M | 综合最强 |
| 省钱日用 | GPT-5 Mini | $0.14/M | 便宜 97%，够用 |
| 写代码 | GPT-5.3 Codex | $3.38/M | 代码专优化 |
| 极速响应 | Gemini 3 Flash | $0.04/M | 毫秒级 |
| 数学推理 | DeepSeek R1 | $0.055/M | 推理王，超便宜 |
| 中文内容 | DeepSeek V3.2 | $0.012/M | 原生中文 |
| 长文档 | Gemini 3 Pro | $1.58/M | 200 万 token 上下文 |

对话中直接说"换成 DeepSeek"就能切换，无需重启。

---

## 小龙虾的技能系统

| 技能 | 功能 | 内置？ |
|------|------|--------|
| 🔍 网页搜索 | 联网搜索实时信息 | ✅ |
| 🌐 浏览器控制 | 操作无头浏览器 | ✅ |
| 💻 代码执行 | 运行 Python/Shell | ✅ |
| 📁 文件管理 | 读写编辑文件 | ✅ |
| 🧠 记忆 | 跨会话持久记忆 | ✅ |
| 🗣️ 语音合成 | 文字转语音 | ✅ |
| 📥 视频下载 | YouTube/Twitter/B站 | 安装扩展 |
| 🌤️ 天气查询 | 全球天气预报 | 安装扩展 |

安装扩展技能：

```bash
openclaw skill install yt-dlp    # 视频下载
openclaw skill install weather   # 天气
```

更多技能：[clawhub.com](https://clawhub.com)

---

## 记忆系统：你的 AI 真的能"记住"

OpenClaw 独特的文件记忆系统：

```
~/.openclaw/workspace/
├── SOUL.md          ← Bot 的性格和行为规则
├── USER.md          ← 关于你的信息
├── MEMORY.md        ← 长期记忆（重要事实）
├── memory/
│   ├── 2026-03-07.md  ← 今天的对话笔记
│   └── 2026-03-06.md  ← 昨天的对话笔记
└── TOOLS.md         ← 工具配置
```

每次启动，Bot 会读取这些文件恢复上下文。你说"记住我喜欢 Python"，它会写入文件，下次启动还记得。

---

## 费用估算

| 使用强度 | 每天消息数 | 月 Token 量 | 月费（Crazyrouter） |
|---------|----------|-----------|-------------------|
| 轻度 | 10-20 条 | ~50 万 | **$2-5** |
| 中度 | 50-100 条 | ~300 万 | **$15-25** |
| 重度 | 200+ 条 | ~1000 万 | **$45-70** |

加上服务器 $5-6/月，**中度使用总费用约 $20-30/月**——和 ChatGPT Plus 差不多，但你拥有 627 个模型、20+ 平台、完整控制权。

---

## 服务器要求

| 配置 | 最低 | 推荐 |
|------|------|------|
| CPU | 1 核 | 2 核+ |
| 内存 | 2 GB | 4 GB |
| 硬盘 | 1 GB | 10 GB |
| 系统 | Linux/macOS | Ubuntu 22+ |
| Node.js | 18+ | 22+ |

$5/月的 VPS 就能跑。

---

## 三个常见误解

### 误解一："自建 AI 很复杂"

一行命令安装，输入两个 Key，5 分钟搞定。不需要懂 Docker、不需要写代码、不需要配 Nginx。

### 误解二："自建比 ChatGPT 贵"

ChatGPT Plus $20/月只能用 GPT。OpenClaw + Crazyrouter 约 $13-30/月，627+ 模型全家桶，还能多平台、自定义、有记忆。

### 误解三："需要为每个模型单独注册"

通过 Crazyrouter，一个 Key 就能调用 OpenAI、Anthropic、Google、DeepSeek、Meta 等 23 家供应商的 627+ 模型。不用分别注册、分别充值。

---

## 快速开始

1. 注册 Crazyrouter → [crazyrouter.com](https://crazyrouter.com?utm_source=csdn&utm_medium=tutorial&utm_campaign=dev_community)（免费送 $0.20）
2. 创建 Telegram Bot → @BotFather
3. 一行命令部署：
   ```bash
   curl -fsSL https://raw.githubusercontent.com/xujfcn/crazyrouter-openclaw/main/install.sh | bash
   ```
4. 开始聊天 → 给 Bot 发 `/start`

**相关链接：**
- OpenClaw 文档：[docs.openclaw.ai](https://docs.openclaw.ai)
- OpenClaw GitHub：[github.com/openclaw/openclaw](https://github.com/openclaw/openclaw)
- Crazyrouter API 文档：[crazyrouter.apifox.cn](https://crazyrouter.apifox.cn)
- 一键部署脚本：[github.com/xujfcn/crazyrouter-openclaw](https://github.com/xujfcn/crazyrouter-openclaw)
- 技能市场：[clawhub.com](https://clawhub.com)

---

*2026 年 3 月 7 日测试验证。模型数量来自 Crazyrouter 实时 API。*
