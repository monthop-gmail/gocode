# Roadmap

## Phase 1 — Core (Done)

- [x] Server mode (`gocode serve` — HTTP + WebSocket)
- [x] CLI client (`gocode chat` — one-shot mode)
- [x] Interactive REPL mode (multi-turn conversations)
- [x] OpenAI-compatible provider (DeepSeek, Qwen, Groq, Ollama, etc.)
- [x] Basic tools: read_file, write_file, list_files, shell
- [x] Docker Compose setup
- [x] .env support for easy configuration

## Phase 2 — More Tools (Done)

- [x] edit_file — partial file editing (find & replace)
- [x] grep — recursive text search in files
- [x] web_search — search the web via DuckDuckGo
- [x] web_fetch — fetch content from URLs
- [x] AGENTS.md support — project-specific instructions
- [x] 21 AGENTS.md templates (coding, IoT, ERP, backoffice, devops)

## Phase 3 — Safety & Persistence

- [ ] Confirmation prompt before dangerous shell commands (rm, git push, etc.)
- [ ] Session persistence (SQLite — keep history across restarts)
- [ ] Rate limiting / token budget per session
- [ ] File access whitelist (restrict which directories agent can access)

## Phase 4 — Smarter Agent

- [ ] Multi-provider support (switch models mid-session)
- [ ] Context management (summarize/compress when token limit is near)
- [ ] Streaming output improvements (syntax highlighting, markdown rendering)
- [ ] Retry with fallback model on failure
- [ ] Cost tracking (estimate token usage per session)

## Phase 5 — Extensibility

- [ ] Plugin system (load custom tools from external Go packages or scripts)
- [ ] MCP (Model Context Protocol) support
- [ ] Hook system (pre/post tool execution hooks)
- [ ] Custom slash commands (/commit, /test, /review)

## Phase 6 — Multi-User & UI

- [ ] Web UI (React/Svelte frontend via existing WebSocket API)
- [ ] Authentication & API keys
- [ ] Multi-user sessions
- [ ] Project workspace isolation (sandboxed per user)
- [ ] VS Code extension

## Ideas (Unscheduled)

- [ ] Git-aware context (auto-include changed files)
- [ ] File watcher (detect external file changes)
- [ ] RAG — index codebase for better search
- [ ] Voice input/output
- [ ] Mobile-friendly web UI
- [ ] Self-hosted LLM optimization (quantized model recommendations)

---

Want to contribute? Pick any unchecked item and open a PR!
