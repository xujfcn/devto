---
title: "3 bước triển khai AI Telegram Bot miễn phí trên Linux: Hướng dẫn từ A đến Z"
published: true
description: "Hướng dẫn chi tiết cách tạo AI Telegram Bot sử dụng GPT-5, Claude, Gemini chỉ với 3 bước đơn giản trên Linux"
tags: ai, telegram, python, vietnamese
canonical_url: https://crazyrouter.com/blog?utm_source=devto&utm_medium=article&utm_campaign=vi_telegram_bot
---

# 3 bước triển khai AI Telegram Bot miễn phí trên Linux

Bạn muốn có một con bot AI trên Telegram — trả lời tin nhắn bằng GPT-5, Claude, hoặc Gemini — nhưng không biết bắt đầu từ đâu?

Bài viết này sẽ hướng dẫn bạn **từ zero đến chạy được** chỉ trong 3 bước, trên bất kỳ máy Linux nào (VPS, Raspberry Pi, hoặc laptop cũ đều được).

**Yêu cầu:**
- Một máy Linux (Ubuntu/Debian khuyến nghị)
- Tài khoản Telegram
- 5 phút rảnh

---

## Bước 1: Tạo Telegram Bot (2 phút)

Mở Telegram, tìm **@BotFather** và gửi lệnh:

```
/newbot
```

BotFather sẽ hỏi bạn:
1. **Tên bot** — đặt gì cũng được, ví dụ: `AI Assistant`
2. **Username** — phải kết thúc bằng `bot`, ví dụ: `myai_helper_bot`

Sau khi tạo xong, BotFather sẽ gửi cho bạn một **API Token** dạng:

```
7123456789:AAHxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

**Lưu lại token này.** Đây là chìa khóa để code của bạn điều khiển bot.

---

## Bước 2: Cài đặt và cấu hình (1 phút)

[OpenClaw](https://github.com/openclaw/openclaw) là framework mã nguồn mở giúp bạn kết nối Telegram Bot với bất kỳ AI model nào chỉ bằng một lệnh.

```bash
# Cài Node.js (nếu chưa có)
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo bash -
sudo apt-get install -y nodejs

# Cài OpenClaw
npm install -g openclaw

# Khởi tạo workspace
openclaw init
```

Cấu hình:

```bash
# Thêm Telegram bot token
openclaw config set telegram.token "YOUR_BOT_TOKEN"

# Thêm AI API key
openclaw config set model "gpt-5"
openclaw config set providers.openai.apiKey "your-api-key"
openclaw config set providers.openai.baseURL "https://crazyrouter.com/v1"
```

> **Mẹo tiết kiệm:** Thay vì đăng ký riêng từng API key của OpenAI, Anthropic, Google, bạn có thể dùng một AI API gateway như [Crazyrouter](https://crazyrouter.com?utm_source=devto&utm_medium=article&utm_campaign=vi_telegram_bot) để truy cập 627+ model với một key duy nhất, giá thấp hơn giá gốc.

---

## Bước 3: Chạy bot (30 giây)

```bash
openclaw start
```

**Xong!** Mở Telegram, gửi tin nhắn cho bot của bạn. Bot sẽ trả lời bằng AI.

---

## Đổi model AI chỉ bằng 1 lệnh

```bash
# Dùng GPT-5
openclaw config set model "gpt-5"

# Đổi sang Claude Opus 4.6
openclaw config set model "claude-opus-4-6"

# Đổi sang Gemini 3 Pro
openclaw config set model "gemini-3-pro-preview"

# Dùng DeepSeek V3.2 (rẻ nhất!)
openclaw config set model "deepseek-v3.2"

openclaw restart
```

Không cần sửa code. Chỉ đổi tên model.

---

## Chạy bot 24/7

```bash
# Dùng systemd
openclaw service install
openclaw service start
openclaw service status
```

---

## So sánh chi phí

| Phương pháp | Chi phí/tháng | Số model | Độ phức tạp |
|-------------|--------------|----------|-------------|
| Đăng ký trực tiếp 3 providers | $60+ | 3 providers | Cao (3 SDK, 3 keys) |
| Dùng API gateway | Theo dùng (từ $0) | 627+ models | Thấp (1 key) |
| Self-host LiteLLM | $5-20 (VPS) | Tùy config | Trung bình |

---

## Tính năng nâng cao

### Memory (bot nhớ ngữ cảnh)

```bash
openclaw config set memory.enabled true
```

### Kết nối nhiều platform

OpenClaw hỗ trợ Telegram, Discord, Slack, WhatsApp.

---

## Xử lý lỗi thường gặp

**Bot không phản hồi:**
```bash
openclaw logs
openclaw status
```

**Test API key:**
```bash
curl https://crazyrouter.com/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer your-key" \
  -d '{"model":"gpt-5","messages":[{"role":"user","content":"Hello"}]}'
```

---

## FAQ

**Q: Có cần VPS không?**
Chạy trên laptop được, nhưng bot chỉ hoạt động khi laptop bật. Khuyến nghị VPS giá rẻ ($3-5/tháng).

**Q: Bot có thể trả lời bằng tiếng Việt không?**
Có! Tất cả model lớn đều hỗ trợ tiếng Việt.

**Q: Chi phí API khoảng bao nhiêu?**
Cá nhân dùng vài chục tin nhắn/ngày, thường dưới $1/tháng. DeepSeek V3.2 gần như miễn phí.

**Q: Có thể dùng cho group chat không?**
Có! Bot phản hồi khi được mention (@bot_name).

**Q: Dữ liệu chat có bị lưu ở đâu không?**
OpenClaw chạy trên máy của bạn. Dữ liệu lưu local, không gửi đến server nào ngoài AI API provider.

---

| Bước | Thời gian | Việc cần làm |
|------|-----------|-------------|
| 1 | 2 phút | Tạo bot trên BotFather |
| 2 | 1 phút | Cài OpenClaw + cấu hình |
| 3 | 30 giây | `openclaw start` |

Tổng cộng chưa đến 5 phút!
