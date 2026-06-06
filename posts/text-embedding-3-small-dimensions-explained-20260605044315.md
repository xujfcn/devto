---
title: 'text-embedding-3-small Dimensions Explained: 1536 vs 1024 vs 512'
published: true
description: 'A practical guide to text-embedding-3-small dimensions: default 1536 vectors, the dimensions parameter, storage tradeoffs, and API examples.'
tags: 'ai, api, embeddings, tutorial'
cover_image: 'https://filesystem.site/cdn/20260605/9d58f0b5e53f10e4e31635c36e5c5e.png'
canonical_url: 'https://crazyrouter.com/blog/text-embedding-3-small-dimensions-explained-20260605044315?utm_source=devto&utm_medium=article&utm_campaign=embedding_dimensions'
id: 3835383
date: '2026-06-06T14:13:22Z'
---

# text-embedding-3-small Dimensions Explained: 1536 vs 1024 vs 512

If you use `text-embedding-3-small`, one small setting can quietly affect your whole retrieval system: embedding dimensions.

The default vector length is **1536 dimensions**. That is a good default. But it is not always the cheapest or fastest choice once you store millions of chunks in a vector database.

This guide explains what `text-embedding-3-small dimensions` means, when to keep 1536, when to test smaller vectors, and how to call an OpenAI-compatible embeddings endpoint with real code.

![text-embedding-3-small dimensions visual guide](https://filesystem.site/cdn/20260605/9d58f0b5e53f10e4e31635c36e5c5e.png)

## What are text-embedding-3-small dimensions?

An embedding turns text into a list of numbers. That list is a vector.

For `text-embedding-3-small`, the default vector has **1536 numbers**. If you embed the sentence:

> “API gateways help developers route model calls.”

The model returns one vector that represents the meaning of that whole input. The vector is not one number per word. It is one semantic representation for the input text you send.

You then store that vector in a vector database such as pgvector, Pinecone, Milvus, Weaviate, Chroma, or Qdrant. When a user searches, you embed the query and compare it against stored vectors.

Official OpenAI documentation states that `text-embedding-3-small` defaults to 1536 dimensions, while `text-embedding-3-large` defaults to 3072 dimensions. It also supports a `dimensions` parameter that can reduce the output vector length.

External references:

- [OpenAI embeddings guide](https://developers.openai.com/api/docs/guides/embeddings?utm_source=crazyrouter_blog&utm_medium=article&utm_campaign=embedding_dimensions)
- [OpenAI text-embedding-3-small model page](https://developers.openai.com/api/docs/models/text-embedding-3-small?utm_source=crazyrouter_blog&utm_medium=article&utm_campaign=embedding_dimensions)
- [Stack Overflow discussion on embedding dimensions](https://stackoverflow.com/questions/75733749/openai-embeddings-api-how-to-change-the-embedding-output-dimension?utm_source=crazyrouter_blog&utm_medium=article&utm_campaign=embedding_dimensions)

## Default text-embedding-3-small dimensions: why 1536 is common

1536 dimensions is popular because it is the default. It is also a practical balance between quality and cost for many semantic search and RAG workloads.

Use the default 1536 dimensions when:

- You are building your first retrieval system.
- You do not have evaluation data yet.
- Your dataset is small enough that vector storage is not painful.
- Search quality matters more than a few gigabytes of storage.
- You want fewer moving parts during the first launch.

That last point matters. If your app is still early, the biggest risk is usually not vector size. It is bad chunking, weak retrieval evaluation, missing metadata filters, or poor prompts.

Start simple. Then optimize.

## The dimensions parameter: what changes and what does not

The `dimensions` parameter lets you request a shorter embedding vector.

For example, instead of asking for the default 1536-dimensional vector, you can request 1024, 768, or 512 dimensions if your provider supports it for that model.

What changes:

| Area | 1536 dimensions | 1024 / 768 / 512 dimensions |
|---|---:|---:|
| Vector storage | Larger | Smaller |
| Index memory | Larger | Smaller |
| Search latency | Often higher | Often lower |
| Retrieval quality | Strong baseline | Must be tested |
| API input token cost | Usually unchanged | Usually unchanged |

What does **not** usually change: the number of input tokens you send. Embedding API pricing is normally based on input tokens, not the final vector size.

That means smaller dimensions mainly help with storage, index memory, and retrieval speed. They are not a magic way to reduce the embedding generation bill.

## Storage math: 1536 vs 1024 vs 512 dimensions

A float32 number uses 4 bytes. So the raw vector size is:

```text
vector_size_bytes = dimensions × 4
```

For one vector:

| Dimensions | Bytes per vector | Storage vs 1536 |
|---:|---:|---:|
| 1536 | 6,144 bytes | Baseline |
| 1024 | 4,096 bytes | ~33% smaller |
| 768 | 3,072 bytes | ~50% smaller |
| 512 | 2,048 bytes | ~67% smaller |

For 1 million chunks, raw float32 vector storage looks like this:

| Dimensions | Raw vector storage | With rough 35% index overhead |
|---:|---:|---:|
| 1536 | ~5.72 GiB | ~7.72 GiB |
| 1024 | ~3.81 GiB | ~5.15 GiB |
| 768 | ~2.86 GiB | ~3.86 GiB |
| 512 | ~1.91 GiB | ~2.57 GiB |

This is why dimensions start to matter at scale. A small difference per vector becomes real infrastructure cost when you store millions of chunks.

## Quick calculator for embedding dimensions

Here is a small Python tool you can use to estimate storage and rough generation cost.

```python
#!/usr/bin/env python3
import argparse


def gib(n):
    return n / (1024 ** 3)


def main():
    parser = argparse.ArgumentParser()
    parser.add_argument("--documents", type=int, required=True)
    parser.add_argument("--avg-tokens", type=int, required=True)
    parser.add_argument("--dimensions", type=int, nargs="+", default=[1536, 1024, 768, 512])
    parser.add_argument("--price-per-million", type=float, default=0.02)
    args = parser.parse_args()

    total_tokens = args.documents * args.avg_tokens
    estimated_cost = total_tokens / 1_000_000 * args.price_per_million

    print(f"Documents: {args.documents:,}")
    print(f"Estimated input tokens: {total_tokens:,}")
    print(f"Embedding generation cost: ${estimated_cost:,.2f}")
    print()
    print("Dims  Raw GiB  With 35% index overhead")

    for dim in args.dimensions:
        raw_bytes = args.documents * dim * 4
        print(f"{dim:>4}  {gib(raw_bytes):>7.2f}  {gib(raw_bytes * 1.35):>24.2f}")


if __name__ == "__main__":
    main()
```

Example:

```bash
python3 embedding_dimension_calculator.py --documents 1000000 --avg-tokens 350
```

Example output:

```text
Embedding Dimension Calculator
================================
Documents/chunks: 1,000,000
Average tokens/chunk: 350
Estimated input tokens: 350,000,000
Embedding generation cost @ $0.02/1M tokens: $7.00

Dimension storage comparison
--------------------------------
  Dims  Bytes/vector     Raw GiB  With index GiB  Saved vs max
  1536         6,144        5.72            7.72            0%
  1024         4,096        3.81            5.15           33%
   768         3,072        2.86            3.86           50%
   512         2,048        1.91            2.57           67%
```

The important lesson: generation cost can stay small, while vector database cost and memory can grow quickly.

## API example: default 1536 dimensions

Here is a standard OpenAI-compatible embeddings call.

```bash
curl https://crazyrouter.com/v1/embeddings \
  -H "Authorization: Bearer $CRAZYROUTER_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "text-embedding-3-small",
    "input": "Explain API gateway routing in one paragraph."
  }'
```

The response includes an embedding array. With the default setting, its length should be 1536.

You can use the same pattern with any OpenAI-compatible client. With Crazyrouter, you only change the base URL and API key:

- Base URL: `https://crazyrouter.com/v1`
- Endpoint: `/embeddings`
- Auth: `Authorization: Bearer YOUR_KEY`

Related internal guides:

- [OpenAI-compatible base URL explained](https://crazyrouter.com/blog/openai-compatible-api-base-url-explained?utm_source=crazyrouter_blog&utm_medium=article&utm_campaign=embedding_dimensions)
- [AI API pricing guide](https://crazyrouter.com/blog/ai-api-pricing-guide-2026?utm_source=crazyrouter_blog&utm_medium=article&utm_campaign=embedding_dimensions)
- [AI API cost optimization guide](https://crazyrouter.com/blog/ai-api-cost-optimization-complete-guide-2026?utm_source=crazyrouter_blog&utm_medium=article&utm_campaign=embedding_dimensions)
- [Structured output JSON mode guide](https://crazyrouter.com/blog/structured-output-json-mode-ai-api-guide-2026?utm_source=crazyrouter_blog&utm_medium=article&utm_campaign=embedding_dimensions)
- [RAG implementation guide](https://crazyrouter.com/blog/rag-implementation-guide-2026?utm_source=crazyrouter_blog&utm_medium=article&utm_campaign=embedding_dimensions)

## Python example: check the vector length

```python
from openai import OpenAI

client = OpenAI(
    api_key="your-crazyrouter-api-key",
    base_url="https://crazyrouter.com/v1",
)

response = client.embeddings.create(
    model="text-embedding-3-small",
    input="A vector database stores embeddings for semantic search.",
)

vector = response.data[0].embedding
print(len(vector))  # usually 1536 by default
print(vector[:5])   # preview the first few values
```

Do not paste real keys into code. Use environment variables in production.

```python
import os
from openai import OpenAI

client = OpenAI(
    api_key=os.environ["CRAZYROUTER_API_KEY"],
    base_url="https://crazyrouter.com/v1",
)
```

## Python example: request custom dimensions

If your embeddings provider supports the `dimensions` parameter for `text-embedding-3-small`, you can request a shorter vector.

```python
from openai import OpenAI

client = OpenAI(
    api_key="your-crazyrouter-api-key",
    base_url="https://crazyrouter.com/v1",
)

response = client.embeddings.create(
    model="text-embedding-3-small",
    input="Shorter embeddings can reduce vector database storage.",
    dimensions=1024,
)

vector = response.data[0].embedding
print(len(vector))  # expected: 1024 when supported
```

Important: do not mix dimensions in the same vector index. If your collection was created for 1536-dimensional vectors, a 1024-dimensional vector will usually fail at insert time.

Use one collection per dimension setting.

## Node.js example: embeddings with OpenAI-compatible base URL

```javascript
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: process.env.CRAZYROUTER_API_KEY,
  baseURL: "https://crazyrouter.com/v1",
});

const response = await client.embeddings.create({
  model: "text-embedding-3-small",
  input: "Embeddings help search by meaning, not just keywords.",
});

const vector = response.data[0].embedding;
console.log(vector.length);
```

## Which dimensions should you choose?

There is no universal best value. Choose based on evaluation, not vibes.

A practical starting point:

| Use case | Suggested starting dimensions | Why |
|---|---:|---|
| Prototype / small app | 1536 | Maximize quality while you learn |
| Support docs RAG | 1536 or 1024 | Quality matters, but storage can grow |
| Large FAQ search | 1024 | Often a good balance to test |
| High-volume semantic cache | 768 or 512 | Speed and memory may matter more |
| Legal / medical / financial retrieval | 1536 | Test carefully before reducing |
| Mobile / edge search | 512 or 768 | Smaller vectors are easier to move |

For production, run an evaluation set. Take 50 to 200 real user queries. Label the best matching documents. Compare recall@5 or recall@10 for 1536, 1024, 768, and 512.

If 1024 gives almost the same recall as 1536, you can reduce storage and memory without hurting users.

## Common mistakes with text-embedding-3-small dimensions

### Mistake 1: mixing 1536 and 1024 vectors in one index

Vector databases expect a fixed dimension per collection or index. If you change dimensions, create a new index and re-embed the corpus.

### Mistake 2: optimizing dimensions before chunking

Bad chunking hurts retrieval more than a larger vector helps it.

Fix chunking first:

- Keep chunks focused.
- Add useful metadata.
- Avoid huge mixed-topic chunks.
- Test overlap instead of guessing.

### Mistake 3: assuming smaller dimensions reduce API cost

Embedding generation cost is usually based on input tokens. Smaller vectors reduce storage and search costs, not necessarily API call cost.

### Mistake 4: choosing 512 without evaluation

512-dimensional vectors can work for some workloads. But they may lose recall on nuanced queries. Test them before moving production search.

### Mistake 5: forgetting downstream schema changes

If you use pgvector, your schema may include a fixed dimension:

```sql
CREATE TABLE documents (
  id bigserial PRIMARY KEY,
  content text,
  embedding vector(1536)
);
```

If you switch to 1024, you need a different column or table:

```sql
CREATE TABLE documents_1024 (
  id bigserial PRIMARY KEY,
  content text,
  embedding vector(1024)
);
```

## A simple evaluation workflow

Use this workflow before changing dimensions in production:

1. Pick 100 real user queries.
2. Label the correct documents for each query.
3. Create separate indexes for 1536, 1024, 768, and 512.
4. Run the same queries against each index.
5. Compare recall@5, recall@10, latency, and memory.
6. Choose the smallest dimension that does not hurt retrieval quality.

This is more reliable than reading a benchmark and hoping it matches your data.

## Final recommendation

For most teams, the best first move is simple:

- Start with `text-embedding-3-small` at 1536 dimensions.
- Build a clean retrieval evaluation set.
- Test 1024 once your corpus grows.
- Try 768 or 512 only when storage, memory, or latency becomes important.

If you already use OpenAI-compatible tools, you can test this through Crazyrouter by setting your base URL to `https://crazyrouter.com/v1` and calling `/embeddings` with your normal SDK.

The goal is not to use the smallest vector. The goal is to use the smallest vector that still retrieves the right answer.

## FAQ: text-embedding-3-small dimensions

### What are the default text-embedding-3-small dimensions?

The default `text-embedding-3-small` output is 1536 dimensions. That means each input text returns a vector with 1536 numeric values.

### Can I change text-embedding-3-small dimensions?

Yes, when your provider supports the `dimensions` parameter, you can request a shorter vector. Common test values are 1024, 768, and 512.

### Do smaller embedding dimensions reduce API cost?

Usually not directly. Embedding API cost is normally based on input tokens. Smaller dimensions mainly reduce vector storage, index memory, and search latency.

### Is 512 dimensions enough for text-embedding-3-small?

Sometimes. It depends on your dataset and retrieval quality requirements. Use an evaluation set before using 512 dimensions in production.

### Can I store 1536 and 1024 dimension vectors in the same database table?

Usually no. Most vector indexes require a fixed dimension. Create separate collections or tables when testing different dimensions.

### Should I use text-embedding-3-small or text-embedding-3-large?

Use `text-embedding-3-small` for cost-effective general retrieval. Test `text-embedding-3-large` when retrieval quality is the main bottleneck and you can afford larger vectors.

### What is the best dimension for RAG?

Start with 1536 for `text-embedding-3-small`. Then test 1024 and 768 against real queries. The best dimension is the smallest one that preserves your recall target.
