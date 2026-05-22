---
title: "One API Key for GPT, Claude, Gemini, Image and Video Models"
published: true
description: "A practical guide to using one OpenAI-compatible gateway for text, image, video, audio, embeddings, and common AI developer tools."
tags: ai, api, tutorial, devtools
canonical_url: https://docs.crazyrouter.com/en/introduction?utm_source=devto&utm_medium=article&utm_campaign=docs_intro
---

Modern AI apps rarely use just one model anymore.

A typical product might use:

- a GPT-style model for chat
- Claude for code review or long-context reasoning
- Gemini for multimodal tasks
- an image model for generation or editing
- a video model for short clips
- embeddings for search
- text-to-speech for voice output

That is powerful, but it can also turn your backend into a pile of provider-specific SDKs, API keys, billing pages, error formats, and environment variables.

This tutorial shows a cleaner pattern: use one API gateway and one API key, while still calling many model families. I will use Crazyrouter as the example because its docs are organized around exactly this workflow: [one unified AI model API gateway for text, image, video, audio, embeddings, rerank, and AI tools](https://docs.crazyrouter.com/en/introduction?utm_source=devto&utm_medium=article&utm_campaign=docs_intro).

The goal is not to hide every model difference. The goal is to keep the boring integration layer simple so you can spend more time on product logic.

## When a unified API key helps

A unified gateway is useful when you want to:

- prototype across several models without rewriting your app
- keep one place for model access and billing controls
- configure tools like Cursor, Claude Code, Codex CLI, Cline, or custom agents consistently
- swap models per task without changing application architecture
- give each project or tool its own token and budget

It is especially helpful for teams building AI features where model choice is still evolving.

For example, you may start with a GPT model for chat, then later discover that a Claude model works better for code explanations, while a Gemini model is better for a multimodal input path. You do not want every experiment to become an infrastructure migration.

## The basic idea

For OpenAI-compatible clients, you usually only change two things:

```txt
API key:  your gateway token
Base URL: https://crazyrouter.com/v1
```

Crazyrouter's documentation introduction summarizes the main entry points here:

- [Docs introduction](https://docs.crazyrouter.com/en/introduction?utm_source=devto&utm_medium=article&utm_campaign=docs_intro)
- [Quick Start](https://docs.crazyrouter.com/en/quickstart?utm_source=devto&utm_medium=article&utm_campaign=docs_intro)
- [API Endpoints](https://docs.crazyrouter.com/en/api-endpoint?utm_source=devto&utm_medium=article&utm_campaign=docs_intro)
- [Authentication](https://docs.crazyrouter.com/en/authentication?utm_source=devto&utm_medium=article&utm_campaign=docs_intro)

The important detail is that OpenAI-compatible SDKs normally expect a `/v1` base URL, while Anthropic-native tools such as Claude Code often expect the root domain. More on that later.

## Step 1: create an API token

Create a token from your gateway dashboard. In a real project, avoid using one all-powerful token everywhere.

A good setup is:

```txt
project-webapp-prod     production app token
project-webapp-dev      development token
claude-code-local       local coding-agent token
cursor-team             editor integration token
video-worker            background media generation token
```

That makes it easier to rotate, budget, and debug usage later.

Store the token in your environment:

```bash
export CRAZYROUTER_API_KEY="sk-your-token-here"
```

For production, use your platform's secret manager instead of committing this value to a `.env` file.

## Step 2: call a GPT-style text model

If you already use the official OpenAI SDK, the integration is intentionally small.

```bash
npm install openai
```

```js
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: process.env.CRAZYROUTER_API_KEY,
  baseURL: "https://crazyrouter.com/v1",
});

const response = await client.chat.completions.create({
  model: "gpt-5.4",
  messages: [
    { role: "system", content: "You write concise technical explanations." },
    { role: "user", content: "Explain API gateways in one paragraph." },
  ],
});

console.log(response.choices[0].message.content);
```

The same pattern works in Python:

```python
from openai import OpenAI
import os

client = OpenAI(
    api_key=os.environ["CRAZYROUTER_API_KEY"],
    base_url="https://crazyrouter.com/v1",
)

response = client.chat.completions.create(
    model="gpt-5.4",
    messages=[
        {"role": "system", "content": "You write concise technical explanations."},
        {"role": "user", "content": "Explain API gateways in one paragraph."},
    ],
)

print(response.choices[0].message.content)
```

The official [Quick Start guide](https://docs.crazyrouter.com/en/quickstart?utm_source=devto&utm_medium=article&utm_campaign=docs_intro) has the minimal cURL, Python, and Node.js examples.

## Step 3: list available models

Before hard-coding model names, build a small diagnostic script that lists the models your token can access.

```js
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: process.env.CRAZYROUTER_API_KEY,
  baseURL: "https://crazyrouter.com/v1",
});

const models = await client.models.list();

for (const model of models.data) {
  console.log(model.id);
}
```

This is useful in CI, internal dashboards, and local setup checks. If a teammate cannot use a model, you can quickly distinguish between:

- the model name is wrong
- the token does not have access
- the base URL is wrong
- the request path is duplicated

The [API Endpoints page](https://docs.crazyrouter.com/en/api-endpoint?utm_source=devto&utm_medium=article&utm_campaign=docs_intro) is worth bookmarking because endpoint path mistakes are a common source of integration bugs.

## Step 4: route different tasks to different models

Once the client is configured, you can choose models per task.

Here is a simplified router function:

```js
const modelByTask = {
  support_chat: "gpt-5.4",
  code_review: "claude-sonnet-4-6",
  lightweight_summary: "gpt-5.4-mini",
};

export async function runTextTask(task, userInput) {
  const model = modelByTask[task];

  if (!model) {
    throw new Error(`Unknown task: ${task}`);
  }

  const response = await client.chat.completions.create({
    model,
    messages: [
      { role: "system", content: `You are handling task: ${task}` },
      { role: "user", content: userInput },
    ],
  });

  return response.choices[0].message.content;
}
```

In production, you might keep this mapping in a config table so you can change models without redeploying.

```json
{
  "support_chat": {
    "model": "gpt-5.4",
    "temperature": 0.3
  },
  "code_review": {
    "model": "claude-sonnet-4-6",
    "temperature": 0.1
  },
  "marketing_draft": {
    "model": "gpt-5.4",
    "temperature": 0.8
  }
}
```

## Step 5: generate images through the same integration layer

Text is only one part of many AI products. You may also need images for thumbnails, product mockups, avatars, ads, or visual brainstorming.

Crazyrouter exposes OpenAI-compatible image generation endpoints such as `/v1/images/generations`. The exact supported model names can change, so check the [image generation docs](https://docs.crazyrouter.com/en/images/gpt-image?utm_source=devto&utm_medium=article&utm_campaign=docs_intro) and your model list.

A typical Node.js request looks like this:

```js
const image = await client.images.generate({
  model: "gpt-image-1.5",
  prompt: "A clean isometric illustration of an API gateway connecting multiple AI models",
  size: "1024x1024",
});

console.log(image.data[0].url ?? image.data[0].b64_json);
```

If your application stores generated media, separate the model call from asset storage:

```js
async function createMarketingImage(prompt) {
  const result = await client.images.generate({
    model: "gpt-image-1.5",
    prompt,
    size: "1024x1024",
  });

  const asset = result.data[0];

  // Save URL or decoded base64 to your object storage here.
  return asset;
}
```

This keeps your application flexible if you later switch from one image model to another.

## Step 6: create video with a worker pattern

Video generation is usually slower than chat completion. Instead of blocking a web request, use a job queue.

A practical architecture:

```txt
User submits prompt
        ↓
API creates video job in database
        ↓
Worker calls video model endpoint
        ↓
Worker polls or receives completion
        ↓
App displays finished video
```

Pseudo-code:

```js
app.post("/api/videos", async (req, res) => {
  const job = await db.videoJob.create({
    prompt: req.body.prompt,
    status: "queued",
  });

  await queue.publish("video.create", { jobId: job.id });

  res.status(202).json({ jobId: job.id });
});
```

Worker:

```js
queue.subscribe("video.create", async ({ jobId }) => {
  const job = await db.videoJob.find(jobId);

  await db.videoJob.update(jobId, { status: "running" });

  // Use the relevant Crazyrouter video endpoint/model here.
  // See the unified video docs for request shape and model-specific options.
  const task = await createVideoWithGateway({
    prompt: job.prompt,
    model: "veo-or-kling-model-name",
  });

  await db.videoJob.update(jobId, {
    status: "submitted",
    providerTaskId: task.id,
  });
});
```

Start from the [Unified Video API docs](https://docs.crazyrouter.com/en/video/unified?utm_source=devto&utm_medium=article&utm_campaign=docs_intro) before designing your production flow.

## Step 7: avoid the most common base URL mistake

This is the integration bug I would check first when someone says, "The same key works in one tool but not another."

Use this rule of thumb:

```txt
OpenAI-compatible SDKs:
https://crazyrouter.com/v1

Claude Code / Anthropic-native clients:
https://crazyrouter.com
```

Do not blindly paste `/v1` into every tool.

For example, an OpenAI SDK app will append `/chat/completions` to the base URL:

```txt
https://crazyrouter.com/v1/chat/completions
```

Claude Code, however, appends the Anthropic Messages path itself. Its base URL should stay at the root domain:

```txt
ANTHROPIC_BASE_URL=https://crazyrouter.com
```

The [API Endpoints docs](https://docs.crazyrouter.com/en/api-endpoint?utm_source=devto&utm_medium=article&utm_campaign=docs_intro) explain this distinction clearly.

## Step 8: use separate tokens for tools and apps

If you connect multiple tools to the same gateway, do not reuse your production app token in every local CLI.

Instead, create separate tokens:

```txt
webapp-production
webapp-staging
claude-code-jeff-laptop
cursor-design-team
video-render-worker
```

Benefits:

- easier rotation
- clearer usage attribution
- safer spending limits
- simpler debugging when one integration breaks
- less blast radius if a local environment leaks

This matters more as your usage moves from experiments to real workflows.

## A small production checklist

Before shipping, check:

- [ ] API key is stored in a secret manager
- [ ] dev, staging, production, and coding tools use separate tokens
- [ ] base URL matches the client type
- [ ] model names are validated at startup or deploy time
- [ ] timeouts are set per task type
- [ ] video and image jobs run asynchronously
- [ ] errors are logged with request IDs, not secret values
- [ ] token budgets or allowlists are configured where appropriate

## Final thoughts

A unified API key does not mean every model behaves identically. You still need to understand model strengths, latency, context windows, media formats, and pricing.

But it does remove a lot of repetitive integration work. For many teams, that is the difference between "we should test another model someday" and "we can test it this afternoon."

If you want to explore the full layout, start with the [Crazyrouter docs introduction](https://docs.crazyrouter.com/en/introduction?utm_source=devto&utm_medium=article&utm_campaign=docs_intro), then follow the task-specific pages for [quick start](https://docs.crazyrouter.com/en/quickstart?utm_source=devto&utm_medium=article&utm_campaign=docs_intro), [API endpoints](https://docs.crazyrouter.com/en/api-endpoint?utm_source=devto&utm_medium=article&utm_campaign=docs_intro), [image generation](https://docs.crazyrouter.com/en/images/gpt-image?utm_source=devto&utm_medium=article&utm_campaign=docs_intro), and [video generation](https://docs.crazyrouter.com/en/video/unified?utm_source=devto&utm_medium=article&utm_campaign=docs_intro).
