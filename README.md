# ChatGPT Codex Proxy

> An Anthropic-compatible `/v1/messages` proxy that lets **Claude Code** talk to the **ChatGPT Codex** backend.

- [한국어 README](./README_ko.md)

## Overview

This project provides an **Anthropic Messages API-compatible** endpoint so you can run Claude Code against ChatGPT's Codex API by setting `ANTHROPIC_BASE_URL`.

```
Claude Code                    chatgpt-codex-proxy              ChatGPT Codex API
   │                               │                                │
   │  POST /v1/messages            │  POST /codex/responses         │
   │  (Anthropic format)           │  (Codex format)                │
   │ ─────────────────────────────>│ ──────────────────────────────>│
   │                               │                                │
   │  Anthropic response           │  Codex SSE response            │
   │ <─────────────────────────────│<──────────────────────────────│
```

## Features

- Anthropic Messages API compatibility (`POST /v1/messages`)
- OAuth 2.0 login for ChatGPT Codex (ChatGPT Plus/Pro required)
- Automatic request/response transformation (Anthropic ↔ Codex)
- SSE streaming support (`stream=true`)
- Claude → Codex model mapping (with env overrides)

## Installation

```bash
git clone <your-repo-url>
cd chatgpt-codex-proxy

npm install
npm run build
```

## Usage

### 1) Login

```bash
npm run login
```

This opens a browser window for OAuth login. You need an active ChatGPT Plus/Pro subscription.

### 2) Run the server

```bash
# Development mode
npm run dev

# Production mode
npm run start
```

### 3) Configure Claude Code

```bash
export ANTHROPIC_BASE_URL=http://127.0.0.1:19080

# token is sourced from either variable (kept for compatibility)
export ANTHROPIC_AUTH_TOKEN=your_chatgpt_oauth_token
export ANTHROPIC_API_KEY="$ANTHROPIC_AUTH_TOKEN"

claude
```

## CLI commands

| Command | Description |
|---|---|
| `npm run login` | OAuth login |
| `npm run logout` | Delete token |
| `npm run status` | Show auth status |
| `npm run dev` | Start dev server (hot reload) |
| `npm run start` | Start production server |

## Model mapping

### Default mapping

| Claude model | Codex model |
|---|---|
| `claude-sonnet-4-20250514` | `gpt-5.2-codex` |
| `claude-3-5-sonnet-20241022` | `gpt-5.2-codex` |
| `claude-3-haiku-20240307` | `gpt-5.3-codex-spark` |
| `claude-3-opus-20240229` | `gpt-5.3-codex-xhigh` |
| (fallback) | `gpt-5.2-codex` |

### Environment overrides

You can override the Codex model used for each Claude family:

| Env var | Description |
|---|---|
| `ANTHROPIC_DEFAULT_HAIKU_MODEL` | Codex model used when a Haiku model is requested |
| `ANTHROPIC_DEFAULT_SONNET_MODEL` | Codex model used when a Sonnet model is requested |
| `ANTHROPIC_DEFAULT_OPUS_MODEL` | Codex model used when an Opus model is requested |
| `PASSTHROUGH_MODE` | If `true/1/yes/on`, pass the requested model name directly to Codex |

Priority: if `PASSTHROUGH_MODE=true` → passthrough; otherwise env overrides → default mapping.

### Optional shell helper

Add this to your `.zshrc`/`.bashrc` to quickly launch Claude Code against the local proxy:

```bash
# Claude Code + ChatGPT Codex proxy (simple mode)
# Start the server separately; this just sets env vars.

gpt() {
  emulate -L zsh

  local proxy_port="${CHATGPT_CODEX_PROXY_PORT:-19080}"
  local token="${ANTHROPIC_AUTH_TOKEN:-${ANTHROPIC_API_KEY:-dummy}}"

  export ANTHROPIC_BASE_URL="http://127.0.0.1:${proxy_port}"
  export ANTHROPIC_AUTH_TOKEN="$token"
  export ANTHROPIC_API_KEY="${ANTHROPIC_API_KEY:-$token}"
  export API_TIMEOUT_MS="${API_TIMEOUT_MS:-90000}"
  export PASSTHROUGH_MODE="${PASSTHROUGH_MODE:-true}"
  unset CLAUDE_CONFIG_DIR

  echo "Using local Codex proxy on :${proxy_port}"
  claude "$@"
}
```

Troubleshooting checklist:

- Health check: `curl -fsS http://127.0.0.1:${CHATGPT_CODEX_PROXY_PORT:-19080}/health`
- Port in use: `lsof -tiTCP:${CHATGPT_CODEX_PROXY_PORT:-19080} -sTCP:LISTEN -nP`
- Passthrough test: `gpt --model gpt-5.2`
- Mapping mode test: `PASSTHROUGH_MODE=false gpt --model gpt-5.2`
- Recent logs: `tail -n 120 /tmp/chatgpt-codex-proxy.log`

Tool-calling smoke test:

- `python3 scripts/tool_calling_smoke.py --base-url http://127.0.0.1:19080 --model gpt-5.2`

## API compatibility

### Supported

| Capability | Support | Notes |
|---|---:|---|
| Basic chat | ✅ | |
| Streaming | ✅ | SSE |
| Multi-turn | ✅ | |
| System prompt | ✅ | Mapped to `instructions` |
| Image input | ⚠️ | Limited |
| Tool calling | ✅ | Bridges `tools/tool_choice/tool_use/tool_result` |
| Temperature | ❌ | Not supported by Codex backend |
| Max tokens | ❌ | Not supported by Codex backend |

Notes:
- Tool calling requires the underlying Codex model/backend to support function calling.
- Anthropic `image(base64)` is transformed into Responses API `input_image` (data URL).
- Multi-turn conversion uses role-aware mapping (`user.text → input_text`, `assistant.text → output_text`).

### Endpoints

- `POST /v1/messages` — Anthropic Messages API
- `GET /health` — Health check

## Project structure

```
chatgpt-codex-proxy/
├── src/
│   ├── index.ts           # Entry point
│   ├── server.ts          # Express server
│   ├── cli.ts             # CLI commands
│   ├── auth.ts            # OAuth login
│   ├── routes/
│   │   └── messages.ts    # /v1/messages endpoint
│   ├── transformers/
│   │   ├── request.ts     # Anthropic → Codex
│   │   └── response.ts    # Codex → Anthropic
│   ├── codex/
│   │   ├── client.ts      # Codex API client
│   │   └── models.ts      # Model mapping
│   ├── types/
│   │   └── anthropic.ts   # Types
│   └── utils/
│       └── errors.ts      # Error handling
├── package.json
├── tsconfig.json
└── README.md
```

## Environment variables

| Variable | Default | Description |
|---|---:|---|
| `PORT` | `19080` | Server port |
| `PROXY_JSON_LIMIT` | `20mb` | JSON body size limit (for image payloads) |
| `CODEX_BASE_URL` | `https://chatgpt.com/backend-api` | Codex API base URL |
| `ANTHROPIC_DEFAULT_HAIKU_MODEL` | - | Haiku → Codex model override |
| `ANTHROPIC_DEFAULT_SONNET_MODEL` | - | Sonnet → Codex model override |
| `ANTHROPIC_DEFAULT_OPUS_MODEL` | - | Opus → Codex model override |
| `PASSTHROUGH_MODE` | `true` | Default passthrough; set `false/0/no/off` to use mapping |

## Security

### Recommended: local-only usage

This project is designed for **personal, local-machine usage**.

```bash
npm run dev  # bind to localhost only
export ANTHROPIC_BASE_URL=http://127.0.0.1:19080
claude
```

### If you deploy this to a server (required hardening)

If you clone/fork and deploy this to any **server / cloud / shared network**, implement all of the following:

1) Add authentication (e.g. API key)
2) Restrict CORS to trusted origins
3) Add rate limiting
4) Lock down token file permissions (`chmod 600 ~/.chatgpt-codex-proxy/tokens.json`)
5) Add monitoring and usage logging

Also: do not bind publicly (avoid `0.0.0.0`) unless you understand the exposure and have implemented the above controls.

## Notes

- Tokens are stored at `~/.chatgpt-codex-proxy/tokens.json`
- Tokens are refreshed automatically (with a 5-minute buffer)
- ChatGPT Plus/Pro subscription is required

## License

MIT
