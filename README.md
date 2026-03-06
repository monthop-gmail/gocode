# gocode

Open source AI coding agent with server mode. No fancy TUI — just a clean server + CLI architecture that any frontend can connect to.

## Features

- **Server-first architecture** — `gocode serve` exposes HTTP + WebSocket API
- **Thin CLI client** — `gocode chat` connects to the server and streams responses
- **OpenAI-compatible** — works with any OpenAI-compatible API (DeepSeek, Qwen, Groq, Together, Ollama, OpenAI)
- **Built-in tools** — read/write files, list directories, execute shell commands
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

> You can also use `config.yaml` for advanced settings (see `config.example.yaml`).

### 3. Chat

```bash
docker compose exec gocode gocode chat \
  --server 127.0.0.1:3000 \
  --config /workspace/config.yaml \
  "list files in current directory"
```

### Build from source (requires Go 1.22+)

```bash
go build -o gocode .
./gocode serve
# In another terminal:
./gocode chat "hello"
```

## Configuration

```yaml
provider:
  base_url: "https://api.deepseek.com/v1"  # Any OpenAI-compatible endpoint
  api_key: "sk-xxx"
  model: "deepseek-chat"

server:
  host: "127.0.0.1"
  port: 3000

agent:
  system_prompt: "You are a helpful coding assistant..."
  max_iterations: 20
```

Environment variables override config: `GOCODE_API_KEY`, `GOCODE_BASE_URL`, `GOCODE_MODEL`

## Supported Providers

| Provider | Base URL |
|----------|----------|
| DeepSeek | `https://api.deepseek.com/v1` |
| OpenAI | `https://api.openai.com/v1` |
| Qwen | `https://dashscope.aliyuncs.com/compatible-mode/v1` |
| Groq | `https://api.groq.com/openai/v1` |
| Together | `https://api.together.xyz/v1` |
| Ollama | `http://localhost:11434/v1` |

## Architecture

```
gocode serve (HTTP + WebSocket server)
    ├── /ws/{sessionID}     WebSocket streaming
    ├── /api/sessions       REST API
    └── /health             Health check
         ↕ WebSocket
gocode chat "prompt"  (thin CLI client)
```

## Project Structure

```
gocode/
├── main.go                    # CLI entrypoint (cobra)
├── internal/
│   ├── config/config.go       # YAML config + env overrides
│   ├── provider/
│   │   ├── provider.go        # LLM interface
│   │   └── openai.go          # OpenAI-compatible implementation
│   ├── tools/
│   │   ├── registry.go        # Tool registration
│   │   ├── read_file.go       # Read file contents
│   │   ├── write_file.go      # Write/create files
│   │   ├── list_files.go      # List directory
│   │   └── shell.go           # Execute shell commands
│   ├── agent/
│   │   ├── agent.go           # Agent loop (LLM ↔ tools)
│   │   └── session.go         # Session management
│   └── server/
│       ├── server.go          # HTTP server setup
│       └── handlers.go        # WebSocket + REST handlers
├── Dockerfile
├── docker-compose.yml
└── config.example.yaml
```

## Contributing

Contributions are welcome! Feel free to open issues and pull requests.

## License

MIT
