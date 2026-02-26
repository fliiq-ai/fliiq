# Fliiq

The AI agent you actually own. One command to plan, build, and automate — with persistent memory and self-generating skills.

## Quick Start

```bash
pip install fliiq
fliiq init                  # Creates ~/.fliiq/ + .env template
# Edit ~/.fliiq/.env with your API key (Anthropic, OpenAI, or Gemini)
fliiq                       # Interactive REPL from any terminal
```

> **Where does `.env` go?**
> - **Pip-installed users:** `~/.fliiq/.env` (created by `fliiq init`) — this is the standard setup.
> - **Internal developers (source checkout):** A project-root `.env` takes precedence over `~/.fliiq/.env`. Fliiq checks for a local `.env` first, then falls back to the global one.

### Project-specific setup (optional)

```bash
cd my-project
fliiq init --project        # Creates .fliiq/ with SOUL.md, playbooks/, mcp.json, etc.
```

### Development install

```bash
git clone <repo-url> && cd Fliiq
pip install -e ".[dev]"
fliiq init
```

### Optional dependencies

Some skills require extra packages that aren't installed by default (to keep the base install lightweight):

| Extra | Install command | Skills enabled |
|-------|----------------|----------------|
| `all` | `pip install "fliiq[all]"` | All optional skill dependencies |
| `browser` | `pip install "fliiq[browser]"` | `web_navigate` — browser automation via browser-use |

Install everything at once or pick what you need:

```bash
pip install "fliiq[all]"               # All optional skill deps
pip install "fliiq[browser]"           # Just browser
```

If you try to use a skill without its dependencies, the agent will tell you exactly what to install.

## Features

**Agent loop** — Claude Code-style architecture where the model plans, executes, and evaluates in a single loop. Three modes: autonomous (full control), supervised (approve each tool call), and plan (plan only, then approve to execute).

**50 core skills** — File I/O (`read_file`, `write_file`, `edit_file`, `list_directory`), search (`grep`, `find`), system (`shell`, `deps`, `dev_server`), web (`web_search`, `fetch_html`, `web_navigate`, `summarize`), memory (`memory_read`, `memory_write`, `memory_search`), email (`send_email`, `receive_emails`, `mark_email_read`, `delete_email`, `archive_email`), SMS (`send_sms`, `receive_sms`), messaging (`send_telegram`, `send_telegram_audio`, `slack`, `discord`, `whatsapp`), voice (`voice_call`), audio (`speech_to_text`, `text_to_speech`), Google Workspace (`google_calendar`, `google_drive`, `google_sheets`, `google_docs`, `google_slides`), Office documents (`excel`, `powerpoint`, `word`), productivity (`tasks`, `contacts`, `reminders`, `notion`, `trello`), project management (`github`), weather (`weather`), image generation (`image_gen`), contacts lookup (`find_email`), time (`get_current_time`), and music (`spotify`).

**Persistent memory** — Curated `MEMORY.md` loaded every session. Daily logs, skill-specific memories, and keyword search across all memory files. The agent reads and updates memory naturally.

**Self-generating skills** — When the agent detects a capability gap, it researches the API, generates a complete skill (SKILL.md + fliiq.yaml + main.py + test_main.py), installs it, and verifies it works before declaring done. Each skill includes a `test_example` in fliiq.yaml with sample params and expected output keys. After install, the agent runs pytest on `test_main.py` and calls the skill as a tool — both must pass. Skills persist across sessions in `.fliiq/skills/`.

**Daemon and jobs** — Background process that runs scheduled tasks. Cron, interval, one-shot, and webhook triggers. Each job gets its own memory file and audit trail. Create jobs via CLI or let the agent create them mid-conversation.

**Customizable identity** — SOUL.md defines the agent's personality. Playbooks add domain-specific instructions. Bundled personas (`--persona product-manager`, `--persona frontend`) activate specialized expertise. All scaffolded from templates and fully editable per-project.

**Email, SMS, and Messaging** — Send and receive email (Gmail OAuth or app password), SMS (Twilio), and messages via Telegram, Slack, Discord, and WhatsApp. Voice calls via Twilio. Two modes: Fliiq's own channels (people message the bot) and managing your accounts (the agent reads your inbox, sends on your behalf).

**Google Workspace** — Full integration via OAuth. Calendar (list/create/update/delete events, attach Google Meet links), Drive (list/search/upload/download/export files, create folders), Sheets (create spreadsheets, read/write cell ranges, append rows), Docs (create/read documents, insert text, batch formatting), and Slides (create/read presentations, add slides, insert text, batch updates). Multi-account support — authorize multiple Google accounts.

**Web navigation** — Browser automation powered by [browser-use](https://github.com/browser-use/browser-use). The agent can navigate websites, extract information, fill forms, and interact with web pages. Runs headless by default or with `--show-browser` to watch it work. Includes a safety checkpoint that pauses for user confirmation before any irreversible action (form submissions, payments, account creation).

**MCP support** — Connect any MCP server (stdio or streamable-http) and its tools are available to the agent alongside built-in skills. One command to add, test, and manage servers.

**Full-screen TUI** — Textual-based interface with message scrolling, mode indicator, thinking timer, and keyboard shortcuts (`fliiq tui`).

**Update notifications** — Fliiq checks PyPI for new versions in the background (zero startup impact). When an update is available, you'll see a one-liner in the REPL, TUI, or after `fliiq run` completes. Disable with `FLIIQ_NO_UPDATE_CHECK=1`.

## Usage

```bash
# Interactive (default)
fliiq                               # REPL (autonomous mode)
fliiq --mode supervised             # REPL in supervised mode
fliiq tui                           # Full-screen TUI

# Single-shot
fliiq run "build a Flask todo app"
fliiq run "what time is it" --mode autonomous
fliiq run "complex task" --model opus-4.6   # Use a specific model
fliiq run "find info on example.com" --show-browser  # Visible browser
fliiq plan "refactor the auth module"

# Personas — specialized expertise
fliiq run "write a PRD for user onboarding" --persona product-manager
fliiq run "build a responsive nav component" --persona frontend
fliiq --persona product-manager         # REPL in PM mode

# Models
fliiq models                        # Show model aliases
fliiq run "task" -m gpt-4.1         # Short flag works too

# Skills
fliiq skill-list                    # Show all skills with source (core/local)
fliiq skill-promote <name>          # Promote local skill to core

# Identity and customization
fliiq soul show                     # Display SOUL.md (default + overrides)
fliiq soul edit                     # Open .fliiq/SOUL.md in $EDITOR
fliiq soul reset                    # Remove overrides, revert to defaults
fliiq playbook list                 # List playbooks with source (custom/bundled)
fliiq playbook show coding          # Display a playbook
fliiq playbook create devops        # Scaffold a new custom playbook

# Google accounts
fliiq google auth                   # Authorize a Google account (Calendar, Gmail, Drive, Sheets, Docs, Slides)
fliiq google accounts               # List authorized accounts

# MCP servers
fliiq mcp add github --command npx --args "@modelcontextprotocol/server-github"
fliiq mcp add my-api --url https://mcp.example.com/mcp
fliiq mcp list                      # Show configured servers
fliiq mcp test                      # Validate connections
fliiq mcp remove github             # Remove a server

# Daemon and jobs
fliiq daemon start                  # Start background daemon
fliiq daemon start --detach         # Detach to background
fliiq daemon status                 # Check if running
fliiq daemon stop                   # Stop daemon

fliiq job list                      # List all jobs
fliiq job create                    # Create a job interactively
fliiq job run <name>                # Manual trigger
fliiq job logs <name>               # View run history
fliiq job delete <name>             # Remove a job
```

### Chat Commands

Inside the REPL (`fliiq`):

| Command   | Action                                         |
|-----------|-------------------------------------------------|
| `/mode`   | Cycle mode (plan -> supervised -> autonomous)  |
| `/status` | Show session info                              |
| `/clear`  | Reset conversation history                     |
| `/help`   | Show available commands                        |
| `/exit`   | Exit chat                                      |

## Architecture

```
fliiq/
  cli/          # Typer CLI, Rich display, Textual TUI, REPL
  runtime/
    agent/      # Agent loop, tool registry, prompt assembly, audit
    llm/        # LLM providers (Anthropic, OpenAI, Gemini) + failover
    skills/     # Skill loader, base class, installer
    mcp/        # MCP client, server connections, tool forwarding
    planning/   # Domain detection, playbook loading, reflection
    memory/     # Memory manager, keyword retrieval
    scheduler/  # Job executor, scheduler, run logging
    browser/  # Browser automation engine (browser-use integration)
  api/          # FastAPI daemon, webhook receiver
  data/         # Bundled skills, SOUL.md, playbooks, templates
```

**Agent loop** (`runtime/agent/loop.py`): Model calls tools, tools return results, loop continues until the model stops or hits max iterations. Mode enforcement filters available tools and gates execution.

**Skill system** (`runtime/skills/`): Each skill is a directory with `SKILL.md` (metadata), `fliiq.yaml` (schema + test_example), `main.py` (async handler), and optionally `test_main.py` (pytest tests). Self-generated skills include mandatory test verification — the agent runs pytest and calls the skill before marking it complete. Discovery scans bundled skills first, then project-level overrides, then user-local `.fliiq/skills/`.

**Memory** (`runtime/memory/`): Files in `.fliiq/memory/`. `MEMORY.md` (curated, always in prompt), daily logs (`YYYY-MM-DD.md`), skill memories (`skills/*.md`). Agent reads/writes via memory skills.

## Configuration

### API Keys (`.env`)

At least one required:

```
ANTHROPIC_API_KEY=your-key
OPENAI_API_KEY=your-key
GEMINI_API_KEY=your-key
```

Priority: Anthropic > OpenAI > Gemini (when multiple keys are set).

### Provider Switching

When multiple API keys are set, use `--provider` to choose which LLM to use:

```bash
fliiq --provider openai               # REPL with OpenAI/GPT-4o
fliiq run "task" --provider gemini     # Single-shot with Gemini
fliiq tui --provider anthropic         # TUI with Claude
```

For a persistent default, set `FLIIQ_PROVIDER` in your `.env`:

```
FLIIQ_PROVIDER=openai
```

Priority: `--provider` flag > `FLIIQ_PROVIDER` env var > first API key found.

### Model Selection

Switch models per-command with `--model` (or `-m`):

```bash
fliiq run "build a Jira skill" --model opus-4.6    # Use Opus for complex tasks
fliiq run "check my inbox" --model sonnet-4.5       # Use Sonnet for daily tasks
fliiq run "task" --model gpt-4.1                    # Use OpenAI GPT-4.1
```

Model aliases are defined in `~/.fliiq/models.yaml` (created by `fliiq init`). View them with:

```bash
fliiq models
```

Default aliases shipped with `fliiq init`:

| Anthropic | OpenAI | Gemini |
|-----------|--------|--------|
| `opus-4.6` | `gpt-4.1` | `gemini-2.5-pro` |
| `sonnet-4.5` | `gpt-4.1-mini` | `gemini-2.5-flash` |
| `sonnet-4.0` | `gpt-4o` | |
| `haiku-4.5` | `gpt-4o-mini` | |
| | `o3` | |
| | `o3-mini` | |
| | `o4-mini` | |

Edit `~/.fliiq/models.yaml` to add your own aliases for Ollama, Mistral, DeepSeek, or any other provider. Use the format `name-version` to stay consistent:

```yaml
# ~/.fliiq/models.yaml
aliases:
  # ... default aliases above ...

  # Custom aliases — add yours here
  mistral-large: mistral-large-latest
  mistral-small: mistral-small-latest
  mistral-codestral: codestral-latest
  deepseek-v3: deepseek-chat
  deepseek-coder: deepseek-coder
  llama-3.2: llama3.2:latest
  qwen-2.5: qwen2.5:latest
```

Then use them like any other alias:

```bash
fliiq run "task" --model mistral-large --provider openai
```

Any string not found in aliases is passed through as-is to the provider API.

Priority: `--model` flag > env var (`ANTHROPIC_MODEL`, etc.) > hardcoded default.

Override the default model persistently via env var:

```
ANTHROPIC_MODEL=claude-opus-4-6
OPENAI_MODEL=gpt-4.1
GEMINI_MODEL=gemini-2.5-flash
```

### Self-Hosted LLMs

Connect any OpenAI-compatible server (Ollama, vLLM, llama.cpp, LM Studio, LocalAI) using `OPENAI_BASE_URL`:

```
OPENAI_API_KEY=not-needed
OPENAI_BASE_URL=http://localhost:11434/v1
OPENAI_MODEL=llama3.2
```

**Quick start with Ollama:**

```bash
# Install and start Ollama (https://ollama.com)
ollama pull llama3.2
ollama serve

# Add to ~/.fliiq/.env
echo 'OPENAI_API_KEY=not-needed' >> ~/.fliiq/.env
echo 'OPENAI_BASE_URL=http://localhost:11434/v1' >> ~/.fliiq/.env
echo 'OPENAI_MODEL=llama3.2' >> ~/.fliiq/.env

# Run with local model
fliiq --provider openai
```

If you also have a cloud API key set, switch between local and cloud with `--provider`:

```bash
fliiq --provider openai      # Local Ollama
fliiq --provider anthropic   # Cloud Claude
```

Tool/skill support (email, calendar, etc.) depends on the model's function calling capability. Most large models (Llama 3.2+, Mistral, Qwen) support it via the OpenAI-compatible API.

`ANTHROPIC_BASE_URL` is also supported for Anthropic-compatible proxies.

### Global Directory (`~/.fliiq/`)

Created by `fliiq init` — used from any terminal:

```
~/.fliiq/
  .env          # API keys + Fliiq channel credentials
  user.yaml     # Your identity (name, emails, timezone)
  models.yaml   # Model aliases for --model flag
  memory/       # Persistent memory files
  audit/        # Audit trails from agent runs
  skills/       # User-generated skills (available everywhere)
```

### Project Directory (`.fliiq/`) — optional

Created by `fliiq init --project` — overrides global for this project:

```
.fliiq/
  SOUL.md       # Agent personality overrides (scaffolded from template)
  playbooks/    # Custom domain playbooks
  mcp.json      # MCP server connections
  memory/       # Project-specific memory
  audit/        # Project audit trails
  jobs/         # Scheduled job definitions (YAML)
  skills/       # Project-specific skills
```

Resolution: local `.fliiq/` > global `~/.fliiq/` > bundled defaults.

### Agent Identity (SOUL.md)

SOUL.md defines the agent's personality, communication style, and behavioral rules. A bundled default ships with the package. To customize for your project:

```bash
fliiq soul edit             # Opens .fliiq/SOUL.md in $EDITOR (creates from template if missing)
fliiq soul show             # See default + your overrides
fliiq soul reset            # Delete overrides, revert to bundled default
```

Your `.fliiq/SOUL.md` is appended to the default as "User Overrides" — you only need to specify what you want to change. The bundled default is never modified.

### Playbooks & Personas

Playbooks are domain-specific instructions loaded into the agent's system prompt. Three come bundled:

| Playbook | Activated by | Focus |
|----------|-------------|-------|
| `coding` | Auto-detected from prompt | Engineering standards, architecture, verification |
| `product-manager` | `--persona product-manager` | PRD writing, ticket writing, prioritization, sprint planning |
| `frontend` | `--persona frontend` | Component architecture, accessibility, visual design quality, performance |

**Explicit activation** — use `--persona` to force a specific playbook:

```bash
fliiq run "write user stories" --persona product-manager
fliiq --persona frontend     # REPL with frontend expertise
```

**Auto-detection** — when no `--persona` is given, Fliiq matches keywords in your prompt. When 2+ keywords match a playbook's domain, it loads automatically.

**Custom playbooks:**

```bash
fliiq playbook create devops    # Scaffold .fliiq/playbooks/devops.md
fliiq playbook list             # Show all (custom + bundled)
fliiq playbook show coding      # Display content
```

Edit the playbook and add trigger keywords on the `# Keywords:` line. User playbooks in `.fliiq/playbooks/` override bundled ones with the same name.

### MCP Servers

Connect external tools via the [Model Context Protocol](https://modelcontextprotocol.io). Fliiq supports stdio and streamable-http transports.

```bash
# Add a stdio server (e.g. from npm)
fliiq mcp add github --command npx --args "@modelcontextprotocol/server-github"

# Add a streamable-http server
fliiq mcp add my-api --url https://mcp.example.com/mcp

# Verify it works
fliiq mcp test github

# List / remove
fliiq mcp list
fliiq mcp remove github
```

MCP tools are auto-discovered at startup and registered alongside built-in skills. The agent sees them as regular tools (prefixed with `mcp_{server}_{tool}`). Connection failures are non-fatal — other servers and skills still work.

Config is stored in `.fliiq/mcp.json` (scaffolded by `fliiq init --project`). You can also edit it directly:

```json
{
  "servers": {
    "github": {
      "command": "npx",
      "args": ["@modelcontextprotocol/server-github"],
      "transport": "stdio"
    }
  }
}
```

### User Profile (`~/.fliiq/user.yaml`)

Your identity file — loaded into every agent session so the agent knows who you are. When you say "my email" or "my calendar," the agent uses these accounts.

```yaml
name: John Doe
emails:
  - address: John@gmail.com
    label: personal
  - address: John@work.com
    label: work
timezone: America/New_York
```

Created by `fliiq init`. Emails are auto-added when you run `fliiq google auth`.

### Google Account Integration

Authorize your Google accounts so the agent can manage your Gmail, Calendar, Drive, Sheets, Docs, and Slides:

```bash
fliiq google auth           # Opens browser for OAuth consent (full Google Workspace)
fliiq google accounts       # List authorized accounts
```

Run `fliiq google auth` once per Google account. Each gets OAuth tokens stored in `~/.fliiq/google_tokens.json`. After auth, you'll be prompted to add the email to your user profile.

**Prerequisites:**
1. Create a project at [Google Cloud Console](https://console.cloud.google.com)
2. Enable **Google Calendar API**, **Gmail API**, **Google Drive API**, **Google Sheets API**, **Google Docs API**, and **Google Slides API**
3. Create OAuth 2.0 credentials (Desktop app, redirect URI `http://localhost:8080/callback`)
4. Add these to your `.env`:

```
GOOGLE_CLIENT_ID=your-client-id
GOOGLE_CLIENT_SECRET=your-client-secret
```

Then ask the agent things like "check my email," "book a meeting tomorrow at 3pm," "list my Drive files," "read the budget spreadsheet," or "add a slide to my presentation."

### Communication Channels

Fliiq supports two distinct use cases for email, SMS, and messaging:

**Use Case 1: Fliiq's own channels (talk TO Fliiq)**

Give Fliiq its own email, phone number, and Telegram bot so people can message it directly — like texting an assistant.

```
# Fliiq's bot email (receives messages on behalf of Fliiq)
FLIIQ_GMAIL_ADDRESS=fliiq-bot@gmail.com
FLIIQ_GMAIL_APP_PASSWORD=your-app-password

# Fliiq's phone number (Twilio)
TWILIO_ACCOUNT_SID=your-sid
TWILIO_AUTH_TOKEN=your-token
TWILIO_PHONE_NUMBER=+1234567890

# Fliiq's Telegram bot
TELEGRAM_BOT_TOKEN=your-bot-token
TELEGRAM_ALLOWED_CHAT_IDS=123456789
```

These are Fliiq's OWN inboxes. The daemon monitors them for inbound messages and responds automatically.

**Telegram Setup**

1. Create a bot via [@BotFather](https://t.me/BotFather) on Telegram and copy the bot token
2. Add `TELEGRAM_BOT_TOKEN=your-bot-token` to `~/.fliiq/.env`
3. Run the interactive setup to detect your chat ID:
   ```bash
   fliiq telegram setup
   ```
   This will prompt you to send a message to your bot, then auto-detect and save your chat ID.

   **Manual alternative:** Add `TELEGRAM_ALLOWED_CHAT_IDS=<chat_id>` to `~/.fliiq/.env` directly. Comma-separate multiple IDs.

> **Required:** When `TELEGRAM_BOT_TOKEN` is set, `TELEGRAM_ALLOWED_CHAT_IDS` must also be set. The daemon will refuse to start without it — this prevents unauthorized users from accessing your agent.

**Text-to-Speech (MiniMax)**

Generate audio pronunciation and send it via Telegram. Ask Fliiq "how do you say X in Cantonese?" and get back Chinese characters + a playable audio message. Supports Cantonese, Mandarin, English, and other languages via MiniMax TTS.

```
MINIMAX_API_KEY=your-api-key
MINIMAX_GROUP_ID=your-group-id
```

Get these from [minimax.io](https://www.minimax.io). The agent uses `text_to_speech` to generate MP3 audio and `send_telegram_audio` to deliver it via Telegram.

**Use Case 2: Your accounts (Fliiq manages FOR you)**

Authorize your personal/work Google accounts so Fliiq can read your email, send on your behalf, and manage your calendar.

```bash
fliiq google auth           # Authorize each Google account via OAuth
```

Emails go in `~/.fliiq/user.yaml`, and the agent uses OAuth (not app passwords) to access them. This is what powers "check my email" and "schedule a meeting."

**The two use cases are independent.** You can set up one, both, or neither. The agent's system prompt distinguishes between "Fliiq's email" (from `.env`) and "the user's email" (from `user.yaml`).

### Troubleshooting

```bash
fliiq doctor                # Verify setup: API keys, SOUL.md, playbooks, MCP, skills
```

## Security

Fliiq gives an LLM agent access to your filesystem, email, SMS, Telegram, and shell commands. That power is the point — but it comes with real risks.

### What Fliiq protects

- **Credential file deny list** — The agent cannot read or write `~/.fliiq/.env`, `~/.fliiq/google_tokens.json`, `~/.fliiq/daemon.secret`, or anything in `~/.ssh/`, `~/.aws/`, `~/.gnupg/`. This prevents prompt injection attacks from exfiltrating secrets.
- **Prompt injection defense** — All inbound external content (Telegram messages, emails, SMS, webhook payloads) is wrapped in `<external_message>` tags with a system prompt instruction telling the agent to never follow instructions from external sources.
- **Telegram allowlist** — `TELEGRAM_ALLOWED_CHAT_IDS` is required when a bot token is set. Unauthorized users get a hardcoded rejection reply — no LLM call, no tool access.
- **Daemon API authentication** — All `/api/*` routes require a Bearer token (auto-generated at `~/.fliiq/daemon.secret`). Prevents local CSRF and rogue processes from triggering agent execution.
- **Package install validation** — The `deps` skill validates package names against a regex and uses `subprocess_exec` (no shell) to prevent command injection.

### What Fliiq does NOT protect

- **Your project files** — The agent has full read/write access to your working directory. This is by design (it needs to edit your code), but a prompt injection attack could modify or delete project files.
- **Self-corruption** — Fliiq can overwrite its own local configuration (`~/.fliiq/jobs/`, `~/.fliiq/user.yaml`, skill files, etc.). If the agent corrupts its local state, reset with:
  ```bash
  rm -rf ~/.fliiq && fliiq init
  ```
  This is safe — core package code lives in `site-packages/` (read-only via pip install). Only local config and job definitions are lost.
- **System prompt extraction** — An attacker with access to the agent can extract the system prompt. This is a soft defense only (LLMs can be jailbroken).
- **Audit log contents** — Audit logs in `~/.fliiq/audit/` may contain sensitive conversation data.

### Best practices

1. **Use supervised mode for untrusted tasks** — `fliiq run "..." --mode supervised` requires your approval before each tool call.
2. **Review scheduled jobs** — Jobs run autonomously in the daemon. Audit `~/.fliiq/jobs/` to know what's running.
3. **Don't put secrets in prompts** — The agent resolves credentials from env vars and OAuth tokens automatically. Never include passwords in job prompts or Telegram messages.
4. **Back up your project** — Use git. The agent writes files. Commits give you rollback.
5. **Rotate daemon secret after exposure** — Delete `~/.fliiq/daemon.secret` and restart the daemon to regenerate.

## Development

```bash
# Install with dev dependencies only
pip install -e ".[dev]"

# Install with dev + all optional skill dependencies
pip install -e ".[dev,all]"

# Install with dev + specific extras
pip install -e ".[dev,browser]"

# Lint
ruff check fliiq/

# Tests
pytest tests/

# Run specific test file
pytest tests/test_agent_loop.py -v
```

### Tech Stack

- **Python 3.12+**, **Typer** (CLI), **Rich** (display), **Textual** (TUI)
- **FastAPI + uvicorn** (daemon), **croniter** (scheduling)
- **Anthropic / OpenAI / Gemini** SDKs (LLM providers)
- **structlog** (logging), **pytest** (testing), **ruff** (linting)

## License

Copyright (c) 2024-2026 Fliiq AI. All rights reserved.
