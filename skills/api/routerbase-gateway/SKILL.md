---
name: routerbase-gateway
description: Integrate OpenAI-compatible apps with RouterBase. Use when migrating SDK calls, configuring ROUTERBASE_API_KEY, choosing model fallbacks, or calling chat, image, video, and audio endpoints through RouterBase.
---

# RouterBase Gateway

## Purpose
Use [routerbase](https://routerbase.com/) as an OpenAI-compatible gateway for model routing, chat completions, and media generation.

## Inputs to request
- Current client or framework: OpenAI SDK, fetch/curl, LangChain, LlamaIndex, Vercel AI SDK, Cursor, Continue, or another OpenAI-compatible tool.
- Target workload: chat, streaming, tool calling, JSON output, vision, image generation, video generation, audio, or embeddings.
- Environment variable location for `ROUTERBASE_API_KEY`.
- Preferred model, latency/cost/quality constraints, and fallback requirements.

## Workflow
1. Classify the work as migration, new integration, routing plan, media generation, or debugging.
2. Keep credentials server-side. Use `ROUTERBASE_API_KEY`; never paste, print, log, or commit real keys.
3. Preserve OpenAI-compatible request shapes where possible.
4. Set the API base URL to `https://routerbase.com/v1`.
5. Choose a model ID and note whether it must be verified against the live RouterBase catalog.
6. For routing, document primary model, fallback model, retryable errors, non-retryable errors, and a smoke test.
7. For media, choose the endpoint by modality:
   - Image: `POST /v1/images/generations`
   - Video: `POST /v1/videos/generations`
   - Speech: `POST /v1/audio/speech`
   - Audio generation: `POST /v1/audio/generations`
8. Add a minimal backend-only validation command before production use.

## Examples

### Python OpenAI-compatible client
```python
import os
from openai import OpenAI

client = OpenAI(
    api_key=os.environ["ROUTERBASE_API_KEY"],
    base_url="https://routerbase.com/v1",
)

response = client.chat.completions.create(
    model="google/gemini-2.5-flash",
    messages=[{"role": "user", "content": "Write one sentence about model routing."}],
)

print(response.choices[0].message.content)
```

### JavaScript OpenAI-compatible client
```js
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: process.env.ROUTERBASE_API_KEY,
  baseURL: "https://routerbase.com/v1",
});

const response = await client.chat.completions.create({
  model: "google/gemini-2.5-flash",
  messages: [{ role: "user", content: "Create a short RouterBase smoke test." }],
});

console.log(response.choices[0].message.content);
```

### Image generation request
```bash
curl -X POST https://routerbase.com/v1/images/generations \
  -H "Authorization: Bearer $ROUTERBASE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "google/imagen-4",
    "prompt": "A clean product illustration of an AI model routing dashboard",
    "aspect_ratio": "1:1",
    "resolution": "1K"
  }'
```

## Output
- Updated client configuration or request example.
- Safe environment variable guidance for `ROUTERBASE_API_KEY`.
- Primary/fallback model plan when routing is requested.
- Endpoint and sync/async notes for media generation.
- Smoke test steps and assumptions to verify before production.

## Quality bar
- Do not expose real API keys, private prompts, customer data, or temporary media URLs.
- Mark model IDs, prices, and feature support as live-catalog checks when they are not verified.
- Retry only transient failures such as timeouts, network errors, `429`, or `5xx`.
- Do not retry invalid credentials, invalid model IDs, validation errors, or policy errors.
- Keep examples OpenAI-compatible unless the user asks for a framework-specific adapter.
