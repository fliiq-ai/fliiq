# Fliiq

The AI agent that **builds itself**.

Manages your email, runs your calendar, automates your workflows, and builds new skills when it needs them. A personal AI that actually does things — all from your terminal.

> Local-first. Bring your own keys.

## Quick Start

```bash
pip install fliiq
fliiq init
fliiq
```

## Why Fliiq?

**It evolves** — When Fliiq can't do something, it researches the API, writes the skill, and uses it — right in the conversation. Your agent gets more capable every time you use it.

**It learns** — Persistent memory across sessions. MEMORY.md, daily logs, and skill-specific memories carry context forward.

**You own it** — Runs locally. Your API keys, your data. No cloud lock-in, no subscription to a platform you don't control.

## Powerful out of the box. Self-extending when you need more.

**Skills System** — 29 built-in skills. Self-generating: when Fliiq can't do something, it builds the skill. File I/O, search, shell, web search, web navigation, email (Gmail OAuth), SMS (Twilio), Telegram, text-to-speech, Google Calendar, contacts, Spotify, and more.

**Daemon & Jobs** — Background process for cron, interval, one-shot, and webhook-triggered automations. Each job gets its own memory and audit trail.

**Channels** — Give Fliiq its own Gmail, phone number, and Telegram bot. Message it like a person. Text-to-speech for Cantonese, Mandarin, and English audio.

**Memory & Identity** — Persistent memory across sessions. SOUL.md defines personality. Playbooks add expertise. Fully editable per-project.

**Multi-LLM** — Anthropic (Claude), OpenAI (GPT-4), Google (Gemini), and any OpenAI-compatible server (Ollama, vLLM, llama.cpp, LM Studio). Switch providers per-command.

**MCP Integration** — Connect any [Model Context Protocol](https://modelcontextprotocol.io) server and its tools are available alongside built-in skills.

**Web Navigation** — Browser automation powered by [browser-use](https://github.com/browser-use/browser-use). Navigate websites, extract information, fill forms.

## Quick Example

```bash
fliiq run "build a Flask todo app with SQLite"
```

Fliiq decomposes the task, creates files, installs dependencies, starts the dev server, and verifies everything works — all in one command.

## Usage

```bash
# Interactive
fliiq                               # REPL (autonomous mode)
fliiq --mode supervised             # Approve each action
fliiq tui                           # Full-screen TUI

# Single-shot
fliiq run "build a Flask todo app"
fliiq run "check my inbox" --model sonnet-4.5
fliiq run "find info on example.com" --show-browser
fliiq plan "refactor the auth module"

# Models
fliiq models                        # Show model aliases
fliiq run "task" -m gpt-4.1         # Use any model

# Skills
fliiq skill-list                    # Show all skills

# Identity
fliiq soul show                     # Display agent personality
fliiq soul edit                     # Customize per-project
fliiq playbook create devops        # Add domain playbooks

# Google
fliiq google auth                   # Authorize Calendar + Gmail
fliiq google accounts               # List authorized accounts

# MCP
fliiq mcp add github --command npx --args "@modelcontextprotocol/server-github"
fliiq mcp list

# Daemon
fliiq daemon start --detach         # Background daemon
fliiq job create                    # Create scheduled job
fliiq job list                      # List all jobs

# Health check
fliiq doctor                        # Verify setup
```

## Configuration

### API Keys

At least one LLM provider key required in `~/.fliiq/.env`:

```
ANTHROPIC_API_KEY=your-key
OPENAI_API_KEY=your-key
GEMINI_API_KEY=your-key
```

### Optional Integrations

| Integration | Env vars | Enables |
|-------------|----------|---------|
| Google Calendar + Gmail | `GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET` | Email, calendar management |
| Telegram | `TELEGRAM_BOT_TOKEN`, `TELEGRAM_ALLOWED_CHAT_IDS` | Messaging, inbound listener |
| Twilio | `TWILIO_ACCOUNT_SID`, `TWILIO_AUTH_TOKEN`, `TWILIO_PHONE_NUMBER` | SMS send/receive |
| MiniMax TTS | `MINIMAX_API_KEY`, `MINIMAX_GROUP_ID` | Text-to-speech audio |
| Brave Search | `BRAVE_API_KEY` | Web search |
| Hunter.io | `HUNTER_API_KEY` | Email lookup by name |
| Spotify | `SPOTIFY_CLIENT_ID`, `SPOTIFY_CLIENT_SECRET` | Playlist management |

### Model Selection

Switch models per-command with `--model`:

```bash
fliiq run "complex task" --model opus-4.6
fliiq run "quick task" --model haiku-4.5
fliiq run "task" --model gpt-4.1
```

Custom aliases in `~/.fliiq/models.yaml`. Works with Ollama, Mistral, DeepSeek, and any OpenAI-compatible API.

### Self-Hosted LLMs

```
OPENAI_API_KEY=not-needed
OPENAI_BASE_URL=http://localhost:11434/v1
OPENAI_MODEL=llama3.2
```

### Optional Dependencies

Some skills require extra packages:

```bash
pip install "fliiq[all]"       # All optional deps
pip install "fliiq[browser]"   # Browser automation only
```

## Security

Fliiq gives an LLM agent access to your filesystem, email, and shell. That power is the point — but it comes with real risks.

**What Fliiq protects:**
- Credential file deny list (`.env`, tokens, SSH keys, AWS creds)
- Prompt injection defense on all inbound channels
- Telegram allowlist (required when bot token is set)
- Daemon API authentication (auto-generated Bearer token)
- Package install validation (prevents command injection)

**Best practices:**
1. Use supervised mode for untrusted tasks
2. Review scheduled jobs in `~/.fliiq/jobs/`
3. Don't put secrets in prompts
4. Back up with git — the agent writes files
5. Report security issues via email (see [SECURITY.md](SECURITY.md))

## Links

- [Docs](https://docs.fliiq.ai)
- [PyPI](https://pypi.org/project/fliiq/)
- [Changelog](CHANGELOG.md)
- [Report a bug](https://github.com/fliiq-ai/fliiq/issues/new?template=bug_report.yml)
- [Request a feature](https://github.com/fliiq-ai/fliiq/issues/new?template=feature_request.yml)

## License

Copyright (c) 2024-2026 Fliiq AI. All rights reserved. See [LICENSE](LICENSE).
