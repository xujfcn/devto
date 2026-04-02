---
title: 'Xây dựng Telegram AI Bot với OpenClaw + Crazyrouter: Hướng dẫn từ A-Z'
published: true
description: 'AI API guide for Vietnamese developers. 627+ models, 55% savings via Crazyrouter gateway.'
tags: 'ai, vietnamese, api, tutorial'
cover_image: null
canonical_url: null
id: 3325033
date: '2026-03-08T01:34:49Z'
---


**Kết luận ngay:** Bạn có thể tự host một **Telegram AI Bot** cá nhân với khả năng truy cập **627+ mô hình AI**, tự động hóa workflow, và quản lý từ xa — chỉ với **1 dòng lệnh curl** và chi phí từ **320,000-750,000₫/tháng** (VPS $5 + API $8-25).

OpenClaw là AI agent platform chạy trên Telegram, kết hợp với Crazyrouter làm AI API backend. Bài viết này hướng dẫn bạn deploy từ A-Z.

---

## Mục lục

1. [Tại sao tự host AI Bot?](#tại-sao-tự-host-ai-bot)
2. [Yêu cầu hệ thống](#yêu-cầu-hệ-thống)
3. [Deploy bằng 1 dòng lệnh](#deploy-bằng-1-dòng-lệnh)
4. [Cấu hình Crazyrouter làm AI backend](#cấu-hình-crazyrouter)
5. [Bảng model khuyên dùng](#bảng-model-khuyên-dùng)
6. [Hệ thống Skills — Siêu năng lực cho bot](#hệ-thống-skills)
7. [Lệnh quản lý bot](#lệnh-quản-lý-bot)
8. [Chi phí thực tế cho developer VN](#chi-phí-thực-tế)
9. [FAQ](#faq)
10. [3 hiểu lầm phổ biến](#3-hiểu-lầm-phổ-biến)

---

## Tại sao tự host AI Bot?

| Tiêu chí | ChatGPT Bot (official) | Telegram Bot tự host (OpenClaw) |
|---|---|---|
| **Chi phí** | $20/tháng (Plus) | $13-30/tháng (linh hoạt) |
| **Số model** | 1-3 | **627+** |
| **Tùy chỉnh** | Hạn chế | **Không giới hạn** |
| **Quyền riêng tư** | Data qua OpenAI | **Server của bạn** |
| **Skills/Plugins** | GPTs (hạn chế) | **Custom skills** (code, web, file...) |
| **Quản lý từ xa** | ❌ | ✅ SSH, cron, file manager |
| **Multi-user** | ❌ | ✅ Chia sẻ cho team |

---

## Yêu cầu hệ thống

### VPS khuyên dùng cho developer Việt Nam

| VPS Provider | Location | Giá/tháng | Latency từ VN | Khuyên dùng |
|---|---|---|---|---|
| **Vultr** | Tokyo 🇯🇵 | $5 | ~30ms | ⭐ Tốt nhất |
| **Vultr** | Singapore 🇸🇬 | $5 | ~40ms | ⭐ Tốt |
| **DigitalOcean** | Singapore | $6 | ~40ms | Tốt |
| **Linode** | Tokyo | $5 | ~35ms | Tốt |
| **AWS Lightsail** | Tokyo | $5 | ~30ms | OK |

> 💡 **Khuyên dùng: Vultr Tokyo $5/tháng** — RAM 1GB, 25GB SSD, 1TB bandwidth. Đủ cho bot cá nhân hoặc team nhỏ.

### Yêu cầu tối thiểu

- **OS:** Ubuntu 22.04+ / Debian 12+
- **RAM:** 512MB+ (khuyên 1GB)
- **Disk:** 5GB+
- **Node.js:** v18+ (sẽ tự cài)
- **Telegram Bot Token:** Tạo qua [@BotFather](https://t.me/BotFather)

---

## Deploy bằng 1 dòng lệnh

### Bước 1: Tạo Telegram Bot Token

1. Mở Telegram → tìm **@BotFather**
2. Gửi `/newbot`
3. Đặt tên bot (ví dụ: `My AI Assistant`)
4. Đặt username (ví dụ: `my_ai_assistant_bot`)
5. Copy **bot token** (dạng `123456:ABC-DEF...`)

### Bước 2: Lấy Crazyrouter API Key

Đăng ký tại [crazyrouter.com](https://crazyrouter.com?utm_source=viblo&utm_medium=tutorial&utm_campaign=dev_community) → Dashboard → Copy API key.

### Bước 3: Deploy OpenClaw

SSH vào VPS và chạy **1 dòng lệnh duy nhất:**

```bash
curl -fsSL https://raw.githubusercontent.com/openclaw/openclaw/main/install.sh | bash
```

Script sẽ tự động:
- ✅ Cài Node.js (nếu chưa có)
- ✅ Cài OpenClaw
- ✅ Hướng dẫn cấu hình

### Bước 4: Cấu hình

```bash
# Cấu hình Telegram
openclaw config set telegram.token "YOUR_BOT_TOKEN"
openclaw config set telegram.allowedUsers "YOUR_TELEGRAM_ID"

# Cấu hình Crazyrouter làm AI backend
openclaw config set ai.provider "openai-compatible"
openclaw config set ai.baseUrl "https://crazyrouter.com/v1"
openclaw config set ai.apiKey "sk-your-crazyrouter-key"
openclaw config set ai.model "claude-sonnet-4"
```

### Bước 5: Khởi động

```bash
openclaw gateway start
```

🎉 **Done!** Mở Telegram, gửi tin nhắn cho bot. Bot sẽ trả lời bằng AI!

---

## Cấu hình Crazyrouter

### Tại sao chọn Crazyrouter cho OpenClaw?

1. **627+ models** — chuyển đổi model chỉ bằng 1 lệnh
2. **55% giảm giá** — tiết kiệm chi phí vận hành
3. **Node Japan** — latency cực thấp từ VPS Tokyo
4. **OpenAI-compatible** — tích hợp seamless với OpenClaw

### Cấu hình nâng cao

```bash
# Model mặc định (dùng cho chat thường)
openclaw config set ai.model "claude-sonnet-4"

# Model cho coding tasks
openclaw config set ai.codingModel "claude-opus-4-6"

# Model rẻ cho tasks đơn giản
openclaw config set ai.fastModel "gpt-4.1-mini"

# Thinking/reasoning model
openclaw config set ai.thinkingModel "deepseek-r1"
```

---

## Bảng model khuyên dùng

### Chọn model theo use case

| Use Case | Model | Giá Crazyrouter/1M tokens | Chất lượng |
|---|---|---|---|
| **Chat hàng ngày** | Claude Sonnet 4 | $1.35 | ⭐⭐⭐⭐ |
| **Coding** | Claude Opus 4.6 | $6.75 | ⭐⭐⭐⭐⭐ |
| **Trả lời nhanh** | GPT-4.1 mini | $0.18 | ⭐⭐⭐ |
| **Reasoning phức tạp** | DeepSeek R1 | $0.055 | ⭐⭐⭐⭐⭐ |
| **Viết content** | GPT-5.2 | $4.50 | ⭐⭐⭐⭐⭐ |
| **Phân tích ảnh** | Gemini 3 Pro | $1.58 | ⭐⭐⭐⭐ |
| **Tóm tắt dài** | Claude Sonnet 4 | $1.35 | ⭐⭐⭐⭐ |
| **Budget tối thiểu** | DeepSeek R1 | $0.055 | ⭐⭐⭐⭐ |

### Cấu hình model mặc định tối ưu chi phí

```bash
# Setup khuyên dùng cho developer VN — cân bằng chất lượng/giá
openclaw config set ai.model "claude-sonnet-4"        # $1.35 — daily driver
openclaw config set ai.codingModel "claude-opus-4-6"  # $6.75 — khi cần code quality
openclaw config set ai.fastModel "gpt-4.1-mini"       # $0.18 — quick tasks
```

---

## Hệ thống Skills — Siêu năng lực cho bot

OpenClaw không chỉ là chatbot — nó là **AI Agent** với hệ thống Skills có thể mở rộng.

### Skills có sẵn

| Skill | Mô tả | Ví dụ sử dụng |
|---|---|---|
| **Web Search** | Tìm kiếm web real-time | "Tìm tin tức AI mới nhất" |
| **Web Fetch** | Đọc nội dung website | "Tóm tắt bài viết URL này" |
| **Code Execution** | Chạy code trên server | "Chạy script Python này" |
| **File Manager** | Quản lý file trên VPS | "Liệt kê file trong /home" |
| **Cron Jobs** | Lên lịch tự động | "Nhắc tôi mỗi 9h sáng" |
| **Camera/Screen** | Chụp ảnh từ thiết bị | "Chụp screenshot" |
| **Weather** | Thời tiết | "Thời tiết Hà Nội hôm nay" |
| **TTS** | Text-to-speech | "Đọc tin nhắn này" |

### Tạo custom skill

```bash
# Tạo skill mới
openclaw skill create my-vietnam-news

# Edit skill file
nano ~/.openclaw/skills/my-vietnam-news/SKILL.md
```

Ví dụ skill tự động tóm tắt tin tức VN:

```markdown
# my-vietnam-news Skill

Khi user hỏi về tin tức Việt Nam:
1. Tìm kiếm web "tin tức việt nam hôm nay"
2. Lấy 5 bài đầu tiên
3. Tóm tắt mỗi bài trong 2 câu
4. Trả về format bullet list
```

---

## Lệnh quản lý bot

### Lệnh cơ bản

```bash
# Khởi động bot
openclaw gateway start

# Dừng bot
openclaw gateway stop

# Restart
openclaw gateway restart

# Xem status
openclaw gateway status

# Xem logs
openclaw gateway logs

# Xem logs real-time
openclaw gateway logs -f
```

### Quản lý từ Telegram

Gửi trực tiếp trong chat với bot:

| Lệnh | Mô tả |
|---|---|
| `/status` | Xem trạng thái bot |
| `/model` | Xem/đổi model đang dùng |
| `/model claude-opus-4-6` | Chuyển sang Claude Opus |
| `/clear` | Xóa lịch sử hội thoại |
| `/help` | Xem tất cả lệnh |

### Quản lý users

```bash
# Thêm user được phép dùng bot
openclaw config set telegram.allowedUsers "USER_ID_1,USER_ID_2"

# Cho phép tất cả (cẩn thận!)
openclaw config set telegram.allowedUsers "*"
```

---

## Chi phí thực tế cho developer VN

### Breakdown chi phí hàng tháng

| Hạng mục | Chi phí (USD) | Chi phí (VND) |
|---|---|---|
| **VPS Vultr Tokyo** | $5 | ~125,000₫ |
| **API nhẹ** (side project) | $3-8 | ~75,000-200,000₫ |
| **API trung bình** (daily use) | $8-15 | ~200,000-375,000₫ |
| **API nặng** (team/production) | $15-25 | ~375,000-625,000₫ |

### Tổng chi phí theo kịch bản

| Kịch bản | Tổng/tháng (USD) | Tổng/tháng (VND) |
|---|---|---|
| 🟢 **Cá nhân / Học tập** | $8-13 | **~200K-325K₫** |
| 🟡 **Freelancer / Daily use** | $13-20 | **~325K-500K₫** |
| 🔴 **Team nhỏ (3-5 người)** | $20-30 | **~500K-750K₫** |

> 💰 **$13/tháng = 320,000₫** — rẻ hơn ChatGPT Plus ($20), nhưng bạn có **627+ models + tự host + full control + custom skills**.

---

## FAQ

### 1. Cần biết lập trình không?

Không cần. Deploy chỉ cần copy-paste commands. Sử dụng thì chat bình thường trên Telegram.

### 2. Bot có hoạt động khi tắt máy tính không?

Có. Bot chạy trên VPS 24/7. Bạn chat trên điện thoại, máy tính, hay bất cứ đâu có Telegram.

### 3. Có thể cho bạn bè/team dùng chung không?

Có. Thêm Telegram User ID vào `allowedUsers`. Mỗi người có conversation riêng.

---

## 3 hiểu lầm phổ biến

### ❌ Hiểu lầm 1: "Tự host bot phức tạp, cần DevOps engineer"

**Sự thật:** OpenClaw deploy bằng **1 dòng curl**. Không cần Docker, Kubernetes, hay kiến thức DevOps. Cấu hình bằng vài lệnh đơn giản. Nếu bạn biết SSH vào server và copy-paste commands — bạn đủ khả năng.

Update cũng đơn giản:
```bash
npm update -g openclaw
openclaw gateway restart
```

### ❌ Hiểu lầm 2: "Self-host AI bot tốn kém hơn dùng ChatGPT"

**Sự thật:** So sánh thực tế:
- ChatGPT Plus: **$20/tháng** → 1-3 models, giới hạn messages, không API
- OpenClaw + Crazyrouter: **$13/tháng** → **627+ models**, không giới hạn, full API, custom skills

Bạn trả **ít hơn $7/tháng** nhưng được **nhiều hơn gấp trăm lần**.

### ❌ Hiểu lầm 3: "Telegram bot không an toàn cho dữ liệu nhạy cảm"

**Sự thật:** 
- Tin nhắn Telegram được **mã hóa end-to-end** (nếu dùng Secret Chat) hoặc **client-server encryption** (chat thường)
- Bot chạy trên **VPS riêng của bạn** — không qua server bên thứ ba
- API calls qua **HTTPS/TLS** đến Crazyrouter → provider gốc
- Bạn **kiểm soát 100%** dữ liệu — muốn xóa lúc nào cũng được

So với dùng ChatGPT web (data thuộc OpenAI), self-host bot thực ra **an toàn hơn**.

---

## Bắt đầu ngay

1. 🚀 **Deploy OpenClaw:** [github.com/openclaw/openclaw](https://github.com/openclaw/openclaw?utm_source=viblo&utm_medium=tutorial&utm_campaign=dev_community)
2. 🔑 **Lấy API key:** [crazyrouter.com](https://crazyrouter.com?utm_source=viblo&utm_medium=tutorial&utm_campaign=dev_community)
3. 📖 **Tài liệu chi tiết:** [docs.openclaw.ai](https://docs.openclaw.ai?utm_source=viblo&utm_medium=tutorial&utm_campaign=dev_community)
4. 💻 **Code mẫu:** [github.com/xujfcn/crazyrouter-openclaw](https://github.com/xujfcn/crazyrouter-openclaw?utm_source=viblo&utm_medium=tutorial&utm_campaign=dev_community)

---

*Cập nhật tháng 3/2026. Xem [docs.openclaw.ai](https://docs.openclaw.ai?utm_source=viblo&utm_medium=tutorial&utm_campaign=dev_community) cho hướng dẫn mới nhất.*

**Tags:** `openclaw telegram vietnam` `telegram ai bot` `tự host ai bot` `crazyrouter` `ai bot vietnam`
