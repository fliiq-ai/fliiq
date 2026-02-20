# Changelog

All notable changes to Fliiq are documented here.

## [1.7.1] - 2026-02-18

### Added
- Full final response text captured in audit trails for conversation reference

## [1.7.0] - 2026-02-17

### Added
- Text-to-speech skill (MiniMax TTS) — generate audio pronunciation for Cantonese, Mandarin, English, and other languages
- Telegram audio delivery — send voice/audio messages via Telegram
- MiniMax API key support in `.env` template

## [1.6.1] - 2026-02-16

### Added
- Critical flag for `ask_user` enabling smarter autonomous mode behavior

## [1.6.0] - 2026-02-15

### Added
- `[all]` optional dependency group for installing all skill extras at once
- `web_navigate` skill — browser automation via [browser-use](https://github.com/browser-use/browser-use) integration
- Telegram as a job delivery channel
- `--model` CLI flag with user-defined aliases in `~/.fliiq/models.yaml`

## [1.5.0] - 2026-02-14

### Added
- Mandatory test verification for self-generated skills (pytest + tool call must pass)
- Update notification system (checks PyPI in background)

### Security
- Prompt injection defense with `<external_message>` tagging on all inbound channels
- Telegram allowlist requirement (daemon refuses to start without it)
- Daemon API Bearer token authentication
- Credential file deny list
- Command injection fix in deps skill

## [1.1.0] - 2026-02-12

### Added
- Self-hosted LLM support via `OPENAI_BASE_URL` (Ollama, vLLM, llama.cpp, LM Studio)
- Multi-provider tool calling (Anthropic, OpenAI, Gemini)
- `--provider` flag and `FLIIQ_PROVIDER` env var for provider switching

## [1.0.0] - 2026-02-11

### Added
- Default CLI to REPL mode (`fliiq` launches interactive chat)
- `fliiq tui` command for full-screen terminal UI
- Google Calendar integration with 5 actions (list, create, update, delete, availability)
- User profile convention (`~/.fliiq/user.yaml`)
- Unified Google OAuth (`fliiq google auth`) for Calendar + Gmail
- `find_email` skill (Hunter.io)
- Agent identity customization (SOUL.md + playbooks)
- MCP server support (stdio + streamable-http)
- Global `~/.fliiq/` config directory
- `fliiq doctor` health check command

## [0.3.0] - 2026-02-09

### Added
- Multi-account email support (send/receive from multiple Gmail accounts)
- `delete_email` and `archive_email` skills
- Email read/unread filtering
- Job retry on failure (3-attempt backoff + startup re-fire)
- Telegram bot with real-time listener + centralized audit trails
- Daemon restart command
- Inbound email (IMAP) and SMS (Twilio polling) skills
- Outbound email (Gmail SMTP) and SMS (Twilio) skills
- Bundled asset packaging for PyPI distribution
- Plan mode with tool gating
