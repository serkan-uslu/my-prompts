# AI API Wrapper Rules

## 🎯 Purpose
Rules for integrating AI APIs (Anthropic Claude, OpenAI) into production Next.js applications — covering security, cost control, streaming, and provider abstraction.

## 📋 Context
- Next.js 14+ App Router
- Vercel AI SDK as the unified interface
- Anthropic Claude as primary provider (or OpenAI)
- Production deployment on Vercel or similar

## 💬 The Prompt / Rules

```
You are integrating an AI API into a production Next.js application.
Follow these rules for all AI API integration work.

**Security — Non-negotiable**
- API keys are server-side only — never in client code, never in NEXT_PUBLIC_ variables
- All AI calls go through your own API routes — never call the AI API from the browser
- Authenticate users before letting them make AI calls — unauthenticated AI calls = open billing
- Rate limit AI endpoints per user — not just globally

**Cost Control**
- Log every AI call: model, tokens in, tokens out, user ID, timestamp
- Set max_tokens on every request — don't let the model run indefinitely
- Use the cheapest model that meets quality requirements:
  - Simple classification/extraction: claude-haiku-4-5
  - General tasks: claude-sonnet-4-6
  - Complex reasoning: claude-opus-4-6 (expensive — use sparingly)
- Cache AI responses when the input is the same (e.g., document summarization)

**Provider Abstraction**
Use the Vercel AI SDK to stay provider-agnostic:
import { generateText, streamText } from 'ai'
import { anthropic } from '@ai-sdk/anthropic'
// or: import { openai } from '@ai-sdk/openai'

This lets you switch providers by changing one import.

**API Route Pattern**
app/api/ai/{feature}/route.ts — one route per AI feature, not one generic route
This makes it easy to:
- Apply different rate limits per feature
- Log by feature
- Add feature-specific system prompts

**Streaming**
- Stream responses for any generation >1 sentence — don't make users wait
- Use streamText() + toDataStreamResponse() for the server
- Use useChat() or useCompletion() from ai/react for the client
- Show a typing indicator while streaming

**System Prompts**
- Store system prompts in lib/ai/prompts.ts — not inline in route handlers
- System prompts are the most important part of the integration — write them carefully
- Version system prompts: add a comment with the date when you change them
- Test system prompts with edge cases before shipping

**Input Validation**
- Validate and sanitize user input before sending to AI — length limits, content checks
- Don't let users inject system prompts through user input (prompt injection)
- Max input length: set based on context window and cost constraints

**Error Handling**
- Handle rate limit errors (429) with retry-after logic
- Handle context length errors (400) by truncating input and retrying
- Never expose raw API error messages to users — log them, show friendly message
- Circuit breaker: if AI fails N times in a row, disable temporarily and alert

**Monitoring**
Track these metrics:
- Latency (time to first token, time to completion)
- Token usage per request and per user
- Error rate by type
- Cost per user/feature
```

## ⚙️ Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{ai_provider}` | AI provider | `Anthropic` / `OpenAI` |
| `{model_id}` | Model to use | `claude-sonnet-4-6` |
| `{feature_name}` | AI feature name | `summarize`, `chat`, `extract` |

## 📝 Example Output

```typescript
// lib/ai/prompts.ts
export const SUMMARIZE_SYSTEM_PROMPT = `
You are a document summarization assistant.
Summarize the provided text concisely in 3-5 bullet points.
Focus on key facts, decisions, and action items.
Do not include information not present in the text.
` // Updated: 2026-03-02

// app/api/ai/summarize/route.ts
import { streamText } from 'ai'
import { anthropic } from '@ai-sdk/anthropic'
import { SUMMARIZE_SYSTEM_PROMPT } from '@/lib/ai/prompts'

export async function POST(req: Request) {
  const session = await getServerSession()
  if (!session) return Response.json({ error: 'Unauthorized' }, { status: 401 })

  const { text } = await req.json()
  if (!text || text.length > 10000) {
    return Response.json({ error: 'Invalid input' }, { status: 400 })
  }

  const result = await streamText({
    model: anthropic('claude-haiku-4-5-20251001'),
    system: SUMMARIZE_SYSTEM_PROMPT,
    prompt: text,
    maxTokens: 500,
  })

  return result.toDataStreamResponse()
}
```

## 🔄 Variations

- **Without Streaming:** Use `generateText()` for short responses (classification, extraction)
- **With Tool Use:** Add Vercel AI SDK tool definitions for structured output

---

*Last updated: 2026-03-02*
