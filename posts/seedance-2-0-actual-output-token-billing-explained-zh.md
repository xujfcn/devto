---
title: "Seedance 2.0 真实计费说明：为什么不是固定按秒收费？"
slug: "seedance-2-0-actual-output-token-billing-explained-zh"
summary: "用两次 720p/4s 实测任务解释 Seedance 2.0 如何按上游返回的 output tokens 计费，并折算本次任务的每秒成本。"
tags: ai, video, api, pricing
published: true
language: zh
cover_image_url: https://raw.githubusercontent.com/xujfcn/images/main/blog/covers/seedance-2-0-pricing-token-to-second-cost-2026.webp
meta_title: "Seedance 2.0 真实计费说明：为什么不是固定按秒收费？"
meta_description: "用两次 720p/4s 实测任务解释 Seedance 2.0 如何按上游返回的 output tokens 计费，并折算本次任务的每秒成本。"
meta_keywords: Seedance 2.0 billing, Seedance pricing, output tokens, AI video API cost, Crazyrouter
---

# Seedance 2.0 真实计费说明：不是固定秒价，而是按 output tokens 扣费

很多客户会直接问：Seedance 2.0 到底一秒多少钱？严格来说，它不是固定按秒计费，而是在任务完成后按上游返回的 output tokens 计费。秒价只能从具体任务结果反推。

![Seedance 2.0 billing guide](https://raw.githubusercontent.com/xujfcn/images/main/blog/covers/seedance-2-0-pricing-token-to-second-cost-2026.webp)

## 核心结论

The correct billing flow is:

```text
submit task -> task completes -> upstream returns usage tokens -> calculate USD by tokens -> convert to Crazyrouter quota
```

It is **not**:

```text
submit task -> charge directly by requested duration seconds
```

So we should not say:

```text
1 second = fixed N tokens
```

Instead, after the task finishes, we can calculate:

```text
observed tokens/sec = billedTokens / requestedDurationSeconds
observed USD/sec = actualPriceUSD / requestedDurationSeconds
```

This is an observed value for that task, not a universal fixed rate.

## 公开能力边界

Current public capability boundary for `doubao-seedance-2-0` and `doubao-seedance-2-0-fast`:

- `480p` supported
- `720p` supported
- `1080p` is not currently in the public supported range

The measured examples below use `720p` and `4s`.

## 计费规则

Seedance 2.0 billing mainly depends on whether the request contains video input.

| Model | Condition | Billing key | Unit price |
|---|---|---|---|
| `doubao-seedance-2-0` | no video input | `doubao-seedance-2-0:video0` | `46 / 7 USD / 1M tokens` |
| `doubao-seedance-2-0` | with video input | `doubao-seedance-2-0:video1` | `28 / 7 USD / 1M tokens` |
| `doubao-seedance-2-0-fast` | no video input | `doubao-seedance-2-0-fast:video0` | `37 / 7 USD / 1M tokens` |
| `doubao-seedance-2-0-fast` | with video input | `doubao-seedance-2-0-fast:video1` | `22 / 7 USD / 1M tokens` |

`video0` means there is no video reference input. `video1` means the request includes video reference input, such as `reference_video`.

## 最终扣费公式

After a successful task, the system first reads `TotalTokens`. If unavailable, it falls back to `CompletionTokens`.

```text
actualPriceUSD =
  unitPriceUSDPer1MTokens
  * (billedTokens / 1_000_000)
  * quantityMultiplier
  * groupRatio
  * discount
```

Without extra multipliers or discounts:

```text
actualPriceUSD = unitPriceUSDPer1MTokens * billedTokens / 1_000_000
```

Crazyrouter quota conversion:

```text
actualQuota = int(actualPriceUSD * QuotaPerUnit)
QuotaPerUnit = 500000
```

![Seedance 2.0 billing calculator](https://raw.githubusercontent.com/xujfcn/images/main/blog/posts/seedance-2-0-pricing-token-to-second-cost-2026-01.webp)

## 实测案例一：文生视频

Request profile:

- Model: `doubao-seedance-2-0`
- Type: text-to-video
- Input: text only, no image or video reference
- Resolution: `720p`
- Duration: `4s`
- Task ID: `cgt-20260617212928-5s755`
- `completion_tokens = 87300`
- `total_tokens = 87300`

Because there is no video reference input, the billing key is:

```text
billing_key = doubao-seedance-2-0:video0
unitPrice = 46 / 7 = 6.571428 USD / 1M tokens
```

Calculation:

```text
tokensPerSecond = 87300 / 4 = 21825 tokens/s
actualPrice = 87300 / 1000000 * 6.571428 = 0.5736 USD
pricePerSecond = 0.5736 / 4 = 0.1434 USD/s
```

Result: this simple 4-second 720p text-to-video task was observed at about **$0.14/sec**.

## 实测案例二：参考视频生成

Request profile:

- Model: `doubao-seedance-2-0`
- Type: reference-video generation
- Input: text + previous generated video as `reference_video`
- Resolution: `720p`
- Duration: `4s`
- Task ID: `cgt-20260617214300-rsnsx`
- `completion_tokens = 173700`
- `total_tokens = 173700`

Because the request contains video reference input, the billing key is:

```text
billing_key = doubao-seedance-2-0:video1
unitPrice = 28 / 7 = 4.000000 USD / 1M tokens
```

Calculation:

```text
tokensPerSecond = 173700 / 4 = 43425 tokens/s
actualPrice = 173700 / 1000000 * 4.000000 = 0.6948 USD
pricePerSecond = 0.6948 / 4 = 0.1737 USD/s
```

Result: this 4-second 720p reference-video task was observed at about **$0.17/sec**.

## 两个案例对比

| Scenario | Duration | Returned tokens | Tokens/sec | Unit price | Total price | Observed $/sec |
|---|---:|---:|---:|---:|---:|---:|
| Simple text-to-video, no video input | 4s | 87,300 | 21,825 | $6.571428 / 1M tokens | $0.5736 | $0.1434/s |
| Reference video generation, with video input | 4s | 173,700 | 43,425 | $4.000000 / 1M tokens | $0.6948 | $0.1737/s |

This comparison shows why a single per-second price is misleading: video-input requests have a lower unit token price, but may return more tokens, so the final observed per-second cost can still be higher.

![Seedance 2.0 billing workflow](https://raw.githubusercontent.com/xujfcn/images/main/blog/posts/seedance-2-0-pricing-token-to-second-cost-2026-02.webp)

## 怎么给客户解释

Seedance 2.0 没有统一固定的每秒价格。任务完成后，上游会返回真实 output tokens，系统按这些 tokens 计费。我们可以从已完成任务反推出本次任务平均每秒成本，但不能承诺所有任务都是同一个 tokens/秒。

If customers ask why there is no fixed second price, the reason is simple: duration is a task parameter, but the final billing unit is the actual output tokens returned after generation.

## 对内建议

To give customers more stable estimates, collect more real task samples and group them by:

- text-to-video without reference input
- image-to-video
- reference-video generation
- with or without audio
- duration
- prompt complexity
- resolution

Then calculate `p50`, `p75`, and `p90` tokens/sec. This is much more reliable than guessing a universal tokens/sec number.

## Bottom line

所以，对外最稳妥的说法是：Seedance 2.0 按真实 output tokens 结算；在当前 720p/4s 实测里，简单文生视频约 0.14 USD/s，参考视频生成约 0.17 USD/s。

For one API layer across video, image, chat, and other AI models, try [Crazyrouter](https://crazyrouter.com?utm_source=crazyrouter_blog&utm_medium=article&utm_campaign=seedance_actual_billing_20260618).
