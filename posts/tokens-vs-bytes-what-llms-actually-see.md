---
title: "Tokens vs Bytes in AI: What LLMs Actually See When You Type"
published: true
tags: ai, tokenization, llm, tutorial
canonical_url: https://crazyrouter.com/blog/tokens-vs-bytes-what-llms-actually-see?utm_source=devto&utm_medium=article&utm_campaign=tokens_vs_bytes
cover_image: https://raw.githubusercontent.com/xujfcn/images/main/blog/covers/tokens-vs-bytes-what-llms-actually-see.webp
---

You type "你好 Hello" into GPT-5. That's 7 characters. But the model processes it as **2 tokens** — and your bill is based on those tokens, not the characters.

Meanwhile, your computer stores that same text as **12 bytes**.

So what's the difference between bytes, characters, and tokens? Why does AI use tokens instead of raw bytes? And why does the same sentence cost more in Chinese than in English?

## Start at the Bottom: What Is a Byte?

A **byte** is the smallest unit of data your computer stores. One byte = 8 bits = a number from 0 to 255.

When you save text to a file, your computer encodes each character into bytes using **UTF-8**:

| Character | UTF-8 Bytes | Byte Count | Hex |
|-----------|-------------|------------|-----|
| `H` | 72 | 1 | 48 |
| `e` | 101 | 1 | 65 |
| `你` | 228, 189, 160 | 3 | e4 bd a0 |
| `好` | 229, 165, 189 | 3 | e5 a5 bd |
| `🚀` | 240, 159, 154, 128 | 4 | f0 9f 9a 80 |

Key pattern:
- **English letters**: 1 byte each
- **Chinese/Japanese/Korean**: 3 bytes each
- **Emojis**: 4 bytes each

## The Four Levels: Bytes → Characters → Words → Tokens

| Level | "Hello, World" | Count | Description |
|-------|---------------|-------|-------------|
| **Bytes** | `48 65 6c 6c 6f 2c 20 57 6f 72 6c 64` | 12 | Raw storage units |
| **Characters** | `H e l l o , ␣ W o r l d` | 12 | Human-readable letters |
| **Words** | `Hello, World` | 2 | Space-separated units |
| **Tokens** | `Hello` `, ` `World` | 3 | What AI actually processes |

**Tokens are not bytes, not characters, and not words.** They're sub-word units that balance vocabulary size with sequence length.

## Why Not Just Use Bytes or Words?

### Problem with bytes: sequences are too long

The computational cost of transformer attention grows quadratically (O(n²)). Using raw bytes would make sequences 3-4x longer, making AI models unaffordably slow.

### Problem with words: vocabulary explodes

English has 170,000+ common words. Add technical terms, names, URLs, code, and other languages — you'd need millions of vocabulary entries.

### Tokens: the sweet spot

```
"unbelievable" → ["un", "bel", "ievable"]    (3 tokens)
"tokenization" → ["Token", "ization"]         (2 tokens)
"Hello"        → ["Hello"]                    (1 token)
```

Short sequences + small vocabulary (~100K-200K) + no unknown words.

## How BPE Tokenization Works

Most LLMs use **Byte Pair Encoding (BPE)**:

1. Split text into individual characters
2. Count the most frequent adjacent pair
3. Merge that pair into a new token
4. Repeat thousands of times until target vocabulary size

### See it yourself with tiktoken

```python
import tiktoken

enc = tiktoken.get_encoding("o200k_base")
text = "你好 Hello"
tokens = enc.encode(text)
token_strings = [enc.decode([t]) for t in tokens]

print(f"Text: {text}")
print(f"UTF-8 bytes: {len(text.encode('utf-8'))}")
print(f"Tokens ({len(tokens)}): {token_strings}")
```

Output: 12 bytes compressed into just 2 tokens!

## Different Models, Different Tokenizers

| Text | cl100k_base (GPT-4) | o200k_base (GPT-4o/5) | UTF-8 Bytes |
|------|---------------------|----------------------|-------------|
| Hello, how are you today? | 7 | 7 | 25 |
| 你好，请用中文解释一下什么是token | 15 | 9 | 47 |
| こんにちは、トークンとは何ですか？ | 12 | 10 | 51 |

**GPT-5's tokenizer is 40% more efficient for Chinese** than GPT-4.

## Token Costs by Language

| Language | Text | Tokens | Bytes | Bytes/Token |
|----------|------|--------|-------|-------------|
| English | "Hello, how are you today?" | 7 | 25 | 3.6 |
| Chinese | "你好，今天怎么样？" | 5 | 27 | 5.4 |
| Japanese | "こんにちは" | 1 | 15 | 15.0 |
| Korean | "안녕하세요" | 2 | 15 | 7.5 |

Chinese text costs roughly **50% more** than English for the same character count.

### Compare models before committing

```python
from openai import OpenAI

client = OpenAI(
    api_key="your-crazyrouter-key",
    base_url="https://crazyrouter.com/v1"
)

for model in ["gpt-5", "gpt-5-mini", "deepseek-v3.2", "claude-sonnet-4"]:
    response = client.chat.completions.create(
        model=model,
        messages=[{"role": "user", "content": "Explain tokenization in 2 sentences"}],
        max_tokens=100
    )
    usage = response.usage
    print(f"{model}: {usage.prompt_tokens} in / {usage.completion_tokens} out")
```

With [Crazyrouter](https://crazyrouter.com?utm_source=devto&utm_medium=article&utm_campaign=tokens_vs_bytes), one API key gives you access to 627+ models.

## Quick Reference

| Property | Bytes | Characters | Words | Tokens |
|----------|-------|------------|-------|--------|
| **What is it** | Raw storage | Human-readable | Space-separated | Sub-word for AI |
| **"Hello"** | 5 | 5 | 1 | 1 |
| **"你好"** | 6 | 2 | 1 | 1 |
| **AI billing?** | No | No | No | **Yes** |

### Key relationships:
- 1 English token ≈ 4 characters ≈ 0.75 words
- 1,000 tokens ≈ 750 English words

---

*Understanding bytes vs tokens is the first step to controlling your AI costs. Full version with more examples: [Crazyrouter Blog](https://crazyrouter.com/blog/tokens-vs-bytes-what-llms-actually-see?utm_source=devto&utm_medium=article&utm_campaign=tokens_vs_bytes)*
