---
title: 'How to Use the OpenAI API: A Beginner''s Complete Tutorial (2026)'
published: true
description: 'Make your first AI API call in under 5 minutes. Step-by-step guide with tested code examples for GPT-5, Claude, Gemini, and 600+ models.'
tags: 'ai, python, tutorial, beginners'
cover_image: null
canonical_url: null
id: 3323983
date: '2026-03-07T18:27:41Z'
---


> **Summary:** You can make your first AI API call in under 5 minutes. This guide walks you through getting an API key, installing the SDK, making your first request, and building a simple chatbot — with real tested code examples that work today.

---

## Prerequisites

- **Python 3.8+** installed
- A **terminal** or code editor
- An **API key** (we'll get one below)

That's it. No machine learning knowledge required.

---

## Step 1: Get Your API Key (2 minutes)

You have two options:

### Option A: Direct from OpenAI
1. Go to [platform.openai.com](https://platform.openai.com)
2. Sign up or log in
3. Navigate to API Keys → Create new secret key
4. Copy the key (starts with `sk-`)
5. Add payment method (pay-per-use, starts at $5)

### Option B: Through an API Gateway (recommended for beginners)

An API gateway gives you one key that works with **all** AI models (GPT-5, Claude, Gemini, DeepSeek), not just OpenAI. It's also significantly cheaper.

1. Go to [crazyrouter.com](https://crazyrouter.com?utm_source=devto&utm_medium=tutorial&utm_campaign=dev_community)
2. Register (free, includes $0.20 starter credit)
3. Copy your API key from the dashboard
4. Use `base_url="https://crazyrouter.com/v1"` in your code

**Why a gateway?** Same OpenAI API format, but you get access to 627+ models and pay ~55% less. If you're just learning, the free credit is enough for hundreds of test calls.

---

## Step 2: Install the OpenAI SDK (30 seconds)

```bash
pip install openai
```

That's the only dependency. The OpenAI Python SDK works with any OpenAI-compatible endpoint, including gateways.

---

## Step 3: Your First API Call

Create a file called `hello_ai.py`:

```python
from openai import OpenAI

# Option A: Direct OpenAI
# client = OpenAI(api_key="sk-your-openai-key")

# Option B: Through gateway (recommended — cheaper, more models)
client = OpenAI(
    api_key="sk-your-gateway-key",
    base_url="https://crazyrouter.com/v1"
)

response = client.chat.completions.create(
    model="gpt-5.2",
    messages=[
        {"role": "user", "content": "What is an API? Explain in one paragraph."}
    ]
)

print(response.choices[0].message.content)
```

Run it:

```bash
python hello_ai.py
```

**Expected output:**
```
An API (Application Programming Interface) is a set of rules and protocols 
that allows different software applications to communicate with each other. 
Think of it as a waiter in a restaurant — you (the application) tell the 
waiter (the API) what you want, the waiter takes your order to the kitchen 
(the server), and brings back your food (the response). APIs enable developers 
to use functionality from other services without needing to understand their 
internal workings.
```

🎉 Congratulations — you just made your first AI API call!

---

## Step 4: Understanding the Request

Let's break down what happened:

```python
response = client.chat.completions.create(
    model="gpt-5.2",           # Which AI model to use
    messages=[                  # The conversation
        {
            "role": "user",    # Who's speaking (user, system, or assistant)
            "content": "..."   # The actual message
        }
    ]
)
```

### Key parameters

| Parameter | Required | Description | Example |
|-----------|----------|-------------|---------|
| `model` | ✅ | Which model to use | `"gpt-5.2"`, `"claude-opus-4-6"`, `"gemini-3-pro"` |
| `messages` | ✅ | Conversation history | List of role/content dicts |
| `temperature` | ❌ | Creativity (0-2, default 1) | `0.7` for balanced |
| `max_tokens` | ❌ | Maximum response length | `500` for medium responses |
| `stream` | ❌ | Real-time streaming | `True` for chat interfaces |

### The three roles

| Role | Purpose | Example |
|------|---------|---------|
| `system` | Set AI's behavior | "You are a helpful coding tutor" |
| `user` | Human's messages | "How do I sort a list in Python?" |
| `assistant` | AI's previous replies | Used for conversation context |

---

## Step 5: Build a Simple Chatbot (10 minutes)

```python
from openai import OpenAI

client = OpenAI(
    api_key="sk-your-key",
    base_url="https://crazyrouter.com/v1"
)

messages = [
    {"role": "system", "content": "You are a friendly AI assistant. Be concise."}
]

print("Chatbot ready! Type 'quit' to exit.\n")

while True:
    user_input = input("You: ")
    if user_input.lower() == 'quit':
        break
    
    messages.append({"role": "user", "content": user_input})
    
    response = client.chat.completions.create(
        model="gpt-5.2",
        messages=messages,
        temperature=0.7
    )
    
    reply = response.choices[0].message.content
    messages.append({"role": "assistant", "content": reply})
    
    print(f"AI: {reply}\n")
```

This chatbot:
- Maintains conversation history (multi-turn)
- Uses a system prompt for consistent behavior
- Runs in your terminal

---

## Step 6: Try Different Models

The biggest advantage of using a gateway: switch models with one parameter change.

```python
# Compare responses from different models
models = [
    "gpt-5.2",          # OpenAI's flagship
    "claude-opus-4-6",   # Anthropic's best
    "gemini-3-pro",      # Google's latest
    "deepseek-r1",       # Best reasoning model
    "gpt-5-mini",        # Cheapest option
]

question = "What's the best programming language for beginners?"

for model in models:
    response = client.chat.completions.create(
        model=model,
        messages=[{"role": "user", "content": question}],
        max_tokens=200
    )
    print(f"\n{'='*50}")
    print(f"Model: {model}")
    print(f"Answer: {response.choices[0].message.content[:200]}")
```

With a gateway key from Crazyrouter, **all 5 models work with the same code** — no separate API keys or SDKs needed. Through direct providers, you'd need 4 different accounts, 4 API keys, and different SDK configurations.

---

## Step 7: Add Streaming (Real-time Output)

For chat interfaces, streaming shows text as it's generated (like ChatGPT):

```python
stream = client.chat.completions.create(
    model="gpt-5.2",
    messages=[{"role": "user", "content": "Write a short poem about coding"}],
    stream=True
)

for chunk in stream:
    content = chunk.choices[0].delta.content
    if content:
        print(content, end="", flush=True)
print()  # New line at the end
```

---

## Cost Estimation

How much will your experiments cost?

| Activity | Tokens Used | Cost (Direct) | Cost (Gateway) |
|----------|------------|---------------|----------------|
| 100 simple Q&As | ~50K | $0.50 | **$0.23** |
| Build a chatbot (1 hour testing) | ~200K | $2.00 | **$0.90** |
| Process 100 documents | ~1M | $10.00 | **$4.50** |
| Full day of development | ~500K | $5.00 | **$2.25** |

**The free $0.20 credit from Crazyrouter** gets you approximately 44,000 input tokens with GPT-5.2 — enough for ~100 test conversations.

---

## Common Errors and Fixes

| Error | Cause | Solution |
|-------|-------|----------|
| `401 Unauthorized` | Bad API key | Double-check key, ensure no extra spaces |
| `429 Too Many Requests` | Rate limited | Wait 30 seconds, implement retry logic |
| `400 Bad Request` | Malformed request | Check message format (must be list of dicts) |
| `404 Model Not Found` | Wrong model name | Check available models in dashboard |
| `Timeout` | Slow response | Add `timeout=60` to client constructor |

---

## 3 Beginner Misconceptions

### "I need to understand machine learning to use AI APIs"

No. AI APIs are **regular REST APIs**. If you can call any web API (weather, maps, payment), you can call an AI API. The complexity is hidden behind the endpoint.

### "I need a powerful computer"

No. The AI model runs on the provider's servers (or gateway's infrastructure). Your computer just sends HTTP requests. A $200 laptop works fine.

### "It's expensive to learn"

With gateway pricing (~55% off) and free starter credits, learning costs under $5 total. Most beginners spend $1-3 during their entire learning phase.

---

## Next Steps

1. ✅ **Done:** Made your first API call
2. **Try:** Different models (Claude, Gemini, DeepSeek) — same code, same key
3. **Build:** A simple project (email summarizer, code reviewer, study aid)
4. **Learn:** [Prompt engineering](https://crazyrouter.com/blog/ai-prompt-engineering-best-practices-2026) — get better results from the same API
5. **Optimize:** [Cost optimization guide](https://crazyrouter.com/blog/ai-api-cost-optimization-complete-guide-2026) — reduce spending as you scale

**Resources:**
- [Crazyrouter API Docs](https://crazyrouter.apifox.cn) — Complete reference for 627+ models
- [OpenAI SDK Docs](https://platform.openai.com/docs) — Official Python SDK documentation
- [Crazyrouter Blog](https://crazyrouter.com/blog) — 142+ technical tutorials

---

*All code examples tested and verified on March 7, 2026, using Python 3.12 and OpenAI SDK v1.68.*
