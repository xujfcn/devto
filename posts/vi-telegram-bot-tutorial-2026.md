---
title: "3 bước triển khai AI Telegram Bot miễn phí trên Linux: Hướng dẫn từ A đến Z"
published: true
description: "Hướng dẫn chi tiết cách tạo AI Telegram Bot sử dụng GPT-5, Claude, Gemini chỉ với 3 bước trên Linux"
tags: ai, telegram, linux, vietnamese
canonical_url:
cover_image:
---
# 3 bước triển khai AI Telegram Bot miễn phí trên Linux: Hướng dẫn từ A đến Z

Bạn muốn có một con bot AI trên Telegram — trả lời tin nhắn bằng GPT-5, Claude, hoặc Gemini — nhưng không biết bắt đầu từ đâu?

Bài viết này sẽ hướng dẫn bạn **từ zero đến chạy được** chỉ trong 3 bước, trên bất kỳ máy Linux nào (VPS, Raspberry Pi, hoặc laptop cũ đều được).

**Yêu cầu:**
- Một máy Linux (Ubuntu/Debian khuyến nghị)
- Tài khoản Telegram
- 5 phút rảnh


## Bước 2: Cài đặt OpenClaw (1 phút)

[OpenClaw](https://github.com/openclaw/openclaw) là framework mã nguồn mở giúp bạn kết nối Telegram Bot với bất kỳ AI model nào chỉ bằng một lệnh.

Chạy trên terminal:

```bash
# Cài Node.js (nếu chưa có)
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo bash -
sudo apt-get install -y nodejs

# Cài OpenClaw
npm install -g openclaw

# Khởi tạo workspace
openclaw init
```

Sau đó cấu hình:

```bash
# Thêm Telegram bot token
openclaw config set telegram.token "7123456789:AAHxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Thêm AI API key (từ bất kỳ provider nào)
openclaw config set model "gpt-5"
openclaw config set providers.openai.apiKey "your-api-key"
openclaw config set providers.openai.baseURL "https://crazyrouter.com/v1"
```

> **Mẹo tiết kiệm:** Thay vì đăng ký riêng từng API key của OpenAI, Anthropic, Google, bạn có thể dùng một AI API gateway như [Crazyrouter](https://crazyrouter.com?utm_source=viblo&utm_medium=article&utm_campaign=vi_telegram_bot) để truy cập 627+ model với một key duy nhất, giá thấp hơn giá gốc.


## Nâng cấp: Đổi model AI chỉ bằng 1 lệnh

Điểm mạnh của OpenClaw là bạn có thể đổi model AI bất cứ lúc nào:

```bash
# Dùng GPT-5 (OpenAI)
openclaw config set model "gpt-5"

# Đổi sang Claude Opus 4.6 (Anthropic)
openclaw config set model "claude-opus-4-6"

# Đổi sang Gemini 3 Pro (Google)
openclaw config set model "gemini-3-pro-preview"

# Dùng DeepSeek V3.2 (rẻ nhất!)
openclaw config set model "deepseek-v3.2"

# Restart để áp dụng
openclaw restart
```

Không cần sửa code. Không cần cài SDK mới. Chỉ đổi tên model.


## So sánh chi phí: Tự build vs Dùng gateway

| Phương pháp | Chi phí hàng tháng | Số model | Độ phức tạp |
|-------------|-------------------|----------|-------------|
| Đăng ký trực tiếp OpenAI + Anthropic + Google | $60+ (3 subscriptions) | 3 providers | Cao (3 SDK, 3 keys) |
| Dùng API gateway | Theo dùng (từ $0) | 627+ models | Thấp (1 key) |
| Self-host LiteLLM | $5-20/tháng (VPS) | Tùy config | Trung bình |

> Với developer Việt Nam, việc tiết kiệm chi phí API là rất quan trọng. Dùng gateway giúp bạn truy cập nhiều model hơn với chi phí thấp hơn.


## Xử lý lỗi thường gặp

### Bot không phản hồi

```bash
# Kiểm tra log
openclaw logs

# Kiểm tra kết nối Telegram
openclaw status
```

### Lỗi API key

Đảm bảo API key đúng và có credit:

```bash
# Test nhanh
curl https://crazyrouter.com/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer your-key" \
  -d '{"model":"gpt-5","messages":[{"role":"user","content":"Hello"}]}'
```

### Lỗi timeout

Nếu model trả lời chậm, thử đổi sang model nhanh hơn:

```bash
openclaw config set model "gpt-5-mini"  # Nhanh và rẻ hơn
```


## Tổng kết

| Bước | Thời gian | Việc cần làm |
|------|-----------|-------------|
| 1 | 2 phút | Tạo bot trên BotFather |
| 2 | 1 phút | Cài OpenClaw + cấu hình |
| 3 | 30 giây | `openclaw start` |

Tổng cộng chưa đến 5 phút, bạn đã có một AI bot trên Telegram có thể dùng GPT-5, Claude, Gemini, và 600+ model khác.

