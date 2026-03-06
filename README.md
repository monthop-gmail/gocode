# gocode

Open source AI coding agent with server mode. No fancy TUI — just a clean server + CLI architecture that any frontend can connect to.

## Features

- **Server-first architecture** — `gocode serve` exposes HTTP + WebSocket API
- **Interactive REPL** — `gocode chat` for multi-turn conversations with context memory
- **One-shot mode** — `gocode chat "your prompt"` for quick tasks
- **OpenAI-compatible** — works with any OpenAI-compatible API (DeepSeek, Qwen, Groq, Together, Ollama, OpenAI)
- **8 built-in tools** — file ops, code search, web search, shell execution
- **AGENTS.md support** — project-specific instructions loaded automatically
- **21 AGENTS.md templates** — coding, IoT, ERP, backoffice, devops and more
- **Agent loop** — LLM autonomously calls tools until the task is done
- **Session management** — maintain conversation context across messages

## Quick Start

### 1. Clone & configure

```bash
git clone https://github.com/monthop-gmail/gocode.git
cd gocode
cp .env.example .env
# Edit .env with your API key and provider settings
```

### 2. Run with Docker Compose

```bash
docker compose up -d
```

### 3. Chat (Interactive REPL)

```bash
docker compose exec gocode gocode chat \
  --server 127.0.0.1:3000 \
  --config /workspace/config.yaml
```

```
  gocode interactive
  Type your message and press Enter. Commands:
  /quit     Exit
  /clear    Start a new session
  /session  Show current session ID

> list files in current directory
⚡ list_files({"path": "."})
   → main.go, go.mod, internal/...

> search for "TODO" in the project
⚡ grep({"pattern": "TODO", "path": "."})
   → main.go:42: // TODO: add timeout

> fix that TODO
⚡ read_file({"path": "main.go"})
⚡ edit_file({"path": "main.go", ...})
   → Successfully edited main.go

> /quit
Bye!
```

### One-shot mode

```bash
docker compose exec gocode gocode chat \
  --server 127.0.0.1:3000 \
  --config /workspace/config.yaml \
  "explain what main.go does"
```

### Build from source (requires Go 1.22+)

```bash
go build -o gocode .
./gocode serve
# In another terminal:
./gocode chat
```

## Tools

| Tool | Description |
|------|-------------|
| `read_file` | Read file contents |
| `write_file` | Create or overwrite files |
| `edit_file` | Partial edit via find & replace (saves tokens) |
| `list_files` | List directory contents |
| `grep` | Search text in files recursively |
| `web_search` | Search the web via DuckDuckGo (no API key needed) |
| `web_fetch` | Fetch content from a URL |
| `shell` | Execute shell commands |

## AGENTS.md

Place an `AGENTS.md` file in your project root to give gocode project-specific instructions. It will be loaded automatically into the system prompt.

```markdown
# AGENTS.md
- Always respond in Thai
- Use Go conventions
- Write tests for every new function
- Never commit .env files
```

See [examples/agents-md/](examples/agents-md/) for 21 ready-to-use templates:

**Coding:** Go, Next.js, Python FastAPI, Rust, Flutter
**IoT:** Arduino/ESP32, IoT Platform, Smart Farm, ESPHome
**ERP:** Odoo
**Tech:** DevOps, SysAdmin, Data Analysis, Technical Writing, Research
**Business:** HR, CRM, Sales, Marketing, Accounting
**General:** Minimal template

## Configuration

### Option 1: Environment variables (.env)

```bash
GOCODE_API_KEY=sk-xxx
GOCODE_BASE_URL=https://api.deepseek.com/v1
GOCODE_MODEL=deepseek-chat
```

### Option 2: Config file (config.yaml)

```yaml
provider:
  base_url: "https://api.deepseek.com/v1"
  api_key: "sk-xxx"
  model: "deepseek-chat"

server:
  host: "127.0.0.1"
  port: 3000

agent:
  system_prompt: "You are a helpful coding assistant..."
  max_iterations: 20
```

Environment variables always override config file values.

## Supported Providers

| Provider | Base URL | Model example |
|----------|----------|---------------|
| DeepSeek | `https://api.deepseek.com/v1` | `deepseek-chat` |
| OpenAI | `https://api.openai.com/v1` | `gpt-4o` |
| Qwen | `https://dashscope.aliyuncs.com/compatible-mode/v1` | `qwen-plus` |
| Groq | `https://api.groq.com/openai/v1` | `llama-3.3-70b-versatile` |
| Together | `https://api.together.xyz/v1` | `meta-llama/Meta-Llama-3.1-70B-Instruct-Turbo` |
| Ollama | `http://localhost:11434/v1` | `llama3.2` |

## Architecture

```
gocode serve (HTTP + WebSocket server)
    ├── /ws/{sessionID}     WebSocket streaming
    ├── /api/sessions       REST API
    └── /health             Health check
         ↕ WebSocket
gocode chat              (interactive REPL or one-shot)
```

## Project Structure

```
gocode/
├── main.go                        # CLI entrypoint (cobra)
├── internal/
│   ├── config/config.go           # YAML config + env overrides
│   ├── provider/
│   │   ├── provider.go            # LLM interface
│   │   └── openai.go              # OpenAI-compatible implementation
│   ├── tools/
│   │   ├── registry.go            # Tool registration
│   │   ├── read_file.go           # Read file contents
│   │   ├── write_file.go          # Write/create files
│   │   ├── edit_file.go           # Partial file editing
│   │   ├── list_files.go          # List directory
│   │   ├── grep.go                # Search text in files
│   │   ├── web_search.go          # Web search (DuckDuckGo)
│   │   ├── web_fetch.go           # Fetch URL content
│   │   └── shell.go               # Execute shell commands
│   ├── agent/
│   │   ├── agent.go               # Agent loop + AGENTS.md loader
│   │   └── session.go             # Session management
│   └── server/
│       ├── server.go              # HTTP server setup
│       └── handlers.go            # WebSocket + REST handlers
├── examples/
│   └── agents-md/                 # 21 AGENTS.md templates
├── Dockerfile
├── docker-compose.yml
├── .env.example
└── config.example.yaml
```

## Roadmap

See [ROADMAP.md](ROADMAP.md) for the full development plan.

**Next up:**
- Confirmation prompt before dangerous commands
- Session persistence (SQLite)
- Plugin system
- Web UI

## gocode vs adkcode

Both are open-source AI coding agents with the same 8 tools and AGENTS.md support — but built differently:

| | [gocode](https://github.com/monthop-gmail/gocode) | [adkcode](https://github.com/monthop-gmail/adkcode) |
|---|--------|---------|
| Language | Go | Python |
| Framework | Custom HTTP + WebSocket server | Google ADK |
| LLM | Any OpenAI-compatible (DeepSeek, Qwen, Groq, OpenAI, Ollama) | Gemini |
| Interface | CLI REPL + one-shot | Web UI + CLI REPL + API server |
| MCP support | - (planned) | Yes (stdio + SSE) |
| Config | `.env` / `config.yaml` | `.env` |
| Session | In-memory | ADK built-in |
| Deployment | Docker Compose / binary | Docker Compose / `pip install` |
| Lines of code | ~1,500 | ~300 |
| Best for | ใช้กับ LLM provider ที่หลากหลาย | ใช้กับ Gemini + ต้องการ Web UI สำเร็จรูป |

## Contributing

Contributions are welcome! Check the [Roadmap](ROADMAP.md) for ideas and open a PR.

## License

MIT
