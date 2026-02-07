# Anthropic Claude Code Stealth Mode

*Как OpenClaw имитирует Claude Code CLI для работы через OAuth токен*

## Суть

При использовании OAuth токена (`sk-ant-oat-...`) от `claude setup-token`, нужно имитировать Claude Code CLI чтобы Anthropic принял запросы.

## Endpoint

```
POST https://api.anthropic.com/v1/messages
```

## Определение OAuth токена

```typescript
function isOAuthToken(apiKey: string): boolean {
  return apiKey.includes("sk-ant-oat");
}
```

## Headers для Stealth Mode

```typescript
{
  "accept": "application/json",
  "anthropic-dangerous-direct-browser-access": "true",
  "anthropic-beta": "claude-code-20250219,oauth-2025-04-20,fine-grained-tool-streaming-2025-05-14,interleaved-thinking-2025-05-14",
  "user-agent": "claude-cli/2.1.2 (external, cli)",
  "x-app": "cli"
}
```

## System Prompt — ОБЯЗАТЕЛЬНО первым блоком

```typescript
{
  type: "text",
  text: "You are Claude Code, Anthropic's official CLI for Claude.",
  cache_control: { type: "ephemeral" }
}
```

## SDK клиент

```typescript
import Anthropic from "@anthropic-ai/sdk";

const client = new Anthropic({
  apiKey: null,           // НЕ apiKey!
  authToken: oauthToken,  // Используем authToken
  baseURL: "https://api.anthropic.com",
  defaultHeaders: { ... },
  dangerouslyAllowBrowser: true
});
```

## Свои тулы

✅ Можно добавлять любые свои тулы — API не ограничивает
✅ Тулы передаются в параметре `tools` запроса
✅ Claude Code identity нужна только для авторизации, не для тулов

## Полная документация

📄 `docs/anthropic-stealth-mode.md` — детальная инструкция с примерами кода

## Библиотеки

- `@anthropic-ai/sdk` — официальный SDK
- `@mariozechner/pi-ai` — обёртка для стриминга (используется в OpenClaw)

---

*Источник: анализ исходного кода OpenClaw, 2026-02-03*
