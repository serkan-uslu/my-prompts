# Ollama Local AI Prompts

## 🎯 Purpose
Prompts and patterns for working with Ollama (local AI) in development workflows — for code review, generation assistance, and offline AI-powered features in apps.

## 📋 Context
- Ollama running locally at `http://localhost:11434`
- Recommended models: `llama3.2`, `codellama`, `deepseek-coder`
- Used both as a developer tool and as an app feature

## 💬 The Prompt / Rules

### Workflow 1: Ollama as a Developer Tool

```
You are a local AI assistant running via Ollama. For code-related tasks:
- Be concise — don't pad responses with explanation I didn't ask for
- Show code, not descriptions of code
- If something is wrong with my code, say what is wrong first, then show the fix
- If you don't know something, say so — don't hallucinate APIs or functions
- Always mention the model you're using if asked (you are: {model_name})
```

### Workflow 2: Using Ollama in a Next.js App

```
You are integrating Ollama into a Next.js 14+ application.
The Ollama instance runs at http://localhost:11434 in development.
Follow these integration rules:

**API Route Setup**
Create app/api/ai/route.ts as the proxy:
- Never call Ollama directly from the client (CORS, security)
- The API route calls Ollama's REST API
- Stream responses for long generations

**Streaming Pattern**
Use the Vercel AI SDK (ai package) for streaming:
import { streamText } from 'ai'
import { ollama } from 'ollama-ai-provider'

export async function POST(req: Request) {
  const { messages } = await req.json()
  const result = await streamText({
    model: ollama('{model_name}'),
    messages,
  })
  return result.toDataStreamResponse()
}

**Model Selection**
- Code tasks: codellama or deepseek-coder
- General chat: llama3.2
- Fast responses: llama3.2:1b (smaller, faster)
- Document analysis: llama3.2 with large context

**Error Handling**
- Check if Ollama is running before making requests
- Show a clear "AI unavailable" state — not a generic error
- Never block the user's workflow if AI fails — it's an enhancement, not a dependency

**In Production**
- Replace Ollama with an API provider (Anthropic, OpenAI) using the same interface
- Use environment variables to switch: AI_PROVIDER=ollama | anthropic | openai
- The Vercel AI SDK supports provider-switching with minimal code change
```

## ⚙️ Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{model_name}` | Ollama model to use | `llama3.2`, `codellama` |
| `{ollama_url}` | Ollama API base URL | `http://localhost:11434` |

## 📝 Example Output

```typescript
// app/api/ai/route.ts
import { streamText } from 'ai'
import { createOllama } from 'ollama-ai-provider'

const ollama = createOllama({ baseURL: 'http://localhost:11434/api' })

export async function POST(req: Request) {
  const { messages } = await req.json()

  const result = await streamText({
    model: ollama('llama3.2'),
    messages,
    system: 'You are a helpful coding assistant.',
  })

  return result.toDataStreamResponse()
}
```

## 🔄 Variations

- **CLI Tool:** Use Ollama's Node.js SDK directly for scripts and automation
- **Provider Switch:** Pattern for swapping Ollama ↔ Claude ↔ OpenAI with the same interface

---

*Last updated: 2026-03-02*
