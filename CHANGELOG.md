# Changelog

All notable changes to Fliiq are documented here.

## [1.22.2] - 2026-03-05

### Fixed
- **LLM call timeout** — added a configurable timeout on LLM API calls to prevent hung requests from stalling the agent indefinitely. Diagnostic error logging now surfaces the root cause when LLM calls fail.

## [1.22.1] - 2026-03-04

### Fixed
- **Email listener account** — fixed a bug where the daemon email listener was using the wrong Gmail account.

## [1.22.0] - 2026-03-04

### Improved
- **Persistent typing indicator** — the Telegram bot now maintains an active typing indicator throughout the entire agent loop, including long-running tasks. Previously the indicator would time out during extended processing.
- **Job failure notifications** — when a scheduled job exhausts all retries, the daemon automatically sends a Telegram notification so you know immediately when something breaks.

## [1.21.1] - 2026-03-03

### Fixed
- **Streaming LLM responses** — switched the Anthropic LLM client to streaming mode, eliminating 10-minute server timeout errors on long-running agent tasks and jobs.

## [1.21.0] - 2026-03-02

### Added
- **Institutional Knowledge System (IKS)** — capture and retrieve structured knowledge records with four types: `decision` (what was chosen and why), `constraint` (rules that must be respected), `heuristic` (learned best practices), `domain_rule` (business/regulatory rules). Records support code anchoring with drift detection — bind a decision to a specific file and line range, and Fliiq flags it when the code changes. Query by keyword, domain, file, or tags.
- `knowledge_save` skill — save structured knowledge records with rationale, alternatives, constraints, and code anchors
- `knowledge_query` skill — search the knowledge base by keyword, domain, file, or tags; results ranked by relevance and confidence
- `knowledge_update` skill — update or expire existing knowledge records

## [1.20.0] - 2026-03-01

### Added
- **Daemon email listener** — when Gmail credentials are configured, the daemon now polls your inbox every 60 seconds and routes inbound messages to the agent automatically. Disable with `FLIIQ_EMAIL_LISTENER=0`. Daemon status now reports `Email: active/inactive`.
- **Email threading** — `send_email` now supports `in_reply_to`, `references`, `cc`, and `bcc` parameters for proper conversation threading. `receive_emails` extracts `Message-ID`, `In-Reply-To`, `References`, `To`, and `CC` headers.

## [1.19.0] - 2026-03-01

### Added
- **Telegram per-request model override** — switch models mid-conversation without restarting the daemon. Use `/model <name>` to set a model for the session, `/model <name> <prompt>` for a one-shot override, or `/model reset` to revert to default. Supports slash-command and natural language syntax (`use the model opus-4.6 to ...`). Model aliases from `~/.fliiq/models.yaml` are resolved automatically.

### Fixed
- **Telegram double-send** — eliminated duplicate responses when the agent used `send_telegram` tool directly in a session. The listener now detects if the agent already sent a message to the chat and skips the automatic reply.

## [1.18.1] - 2026-02-28

### Fixed
- **`skill-promote` path bug** — `fliiq skill-promote` was writing promoted skills to `<project_root>/skills/core/` instead of the correct bundled package path (`fliiq/data/skills/core/`). Fixed to use `bundled_skills_dir()`.
- **Bundled `fetch_arxiv` and `read_pdf`** — both skills are now bundled as core skills (previously required manual promotion). Available out-of-the-box on fresh installs.

## [1.18.0] - 2026-02-28

### Added
- **Telegram Forum Topics** — `send_telegram` and `send_telegram_audio` now support `message_thread_id` for sending messages and audio to specific topics in Telegram group chats with Forum Topics enabled. Useful for routing job notifications to dedicated threads (e.g., a "releases" topic in a team group).

## [1.17.0] - 2026-02-27

### Added
- **Job playbooks** — jobs can now load a domain playbook into their system prompt via the `playbook:` field in job YAML (e.g. `playbook: blog-seo`). Lets scheduled jobs run with specialized editorial or domain instructions without manual persona flags.
- **Blog SEO persona** — new bundled `blog-seo` playbook with editorial standards, banned phrases list, quality gates, and content type templates. Activate with `--persona blog-seo` or set `playbook: blog-seo` in a job definition.

## [1.16.1] - 2026-02-27

### Improved
- **TUI** — applied Fliiq brand color scheme and fixed status bar layout (`fliiq tui`)

## [1.16.0] - 2026-02-27

### Improved
- **Persistent memory** — Fliiq now automatically extracts institutional knowledge from conversations. After each session, an LLM pass identifies people, ongoing topics, and decisions, then merges them into structured memory files. Context carries forward without any manual effort.

## [1.15.0] - 2026-02-27

### Added
- **Colony** — multi-agent directed mode: give Fliiq a mission and five specialized agents (Intelligence, Research, Scout, QA, Governance) collaborate to deliver results. Run with `fliiq colony start --mission "your objective"`. Supports `--dry-run` for a 15-minute test run and generates a full session report on completion.
- `fetch_arxiv` skill — search arXiv by keyword or retrieve a specific paper by ID; returns metadata, abstracts, and optionally downloads PDFs

### Fixed
- `web_search` skill — exponential backoff retry for Brave API rate limits; reduces failed searches under heavy load

## [1.14.0] - 2026-02-26

### Improved
- TUI overhaul (`fliiq tui`): live thinking timer, incremental tool call display as they execute, and a redesigned status bar

## [1.13.0] - 2026-02-26

### Added
- `excel` skill — create, read, and write Excel (.xlsx) files with support for multiple sheets, cell ranges, and structured data
- `powerpoint` skill — create, read, and modify PowerPoint (.pptx) presentations; add slides, insert text, extract content
- `word` skill — create, read, and modify Word (.docx) documents; add headings, paragraphs, and tables

## [1.12.0] - 2026-02-25

### Added
- `google_calendar` skill — Google Meet link support: pass `add_google_meet: true` when creating or updating calendar events to automatically attach a Google Meet video conference link

## [1.11.0] - 2026-02-25

### Added
- `weather` skill — get current weather and forecasts for any location (no API key required)
- `summarize` skill — extract and clean content from URLs, local files, or raw text for analysis
- `image_gen` skill — generate images from text prompts using OpenAI DALL-E 3
- `slack` skill — send messages, react with emoji, pin messages, list channels, and read message history in Slack workspaces
- `discord` skill — send messages, react, list channels, and read message history in Discord servers
- `whatsapp` skill — send WhatsApp messages and templates via Meta's WhatsApp Business Cloud API
- `voice_call` skill — make outbound voice calls via Twilio with text-to-speech message delivery
- `notion` skill — search, create, read, and update Notion pages and databases
- `github` skill — manage GitHub repositories: list/create issues and PRs, view CI runs, get repo info
- `trello` skill — manage Trello boards, lists, and cards; create/move/archive cards and add comments

## [1.10.0] - 2026-02-25

### Added
- `speech_to_text` skill — transcribe audio files (OGG, MP3, M4A, WAV, WEBM) to text using OpenAI Whisper API
- Telegram voice message support — send a voice note to the bot and it transcribes, processes, and responds; replies can be delivered as audio via TTS
- Typing indicator shown in Telegram while the agent is processing

## [1.9.0] - 2026-02-23

### Added
- Tasks skill — create, update, list, complete, and delete personal tasks with priorities, due dates, tags, and project grouping (`tasks`)
- Contacts skill — manage contacts and relationships, log interactions, and track deals through a sales pipeline (`contacts`)
- Reminders skill — set, list, and cancel one-time or recurring reminders delivered via Telegram, email, or SMS (`reminders`)

## [1.8.0] - 2026-02-20

### Added
- Google Drive skill — list, search, upload, download, export files, create folders (`google_drive`)
- Google Sheets skill — create spreadsheets, read/write cell ranges, append rows (`google_sheets`)
- Google Docs skill — create/read documents, insert text, batch formatting (`google_docs`)
- Google Slides skill — create/read presentations, add slides, insert text, batch updates (`google_slides`)
- OAuth scope expansion for all new Google services via `fliiq google auth`

## [1.7.3] - 2026-02-21

### Fixed
- Resolved all 61 ruff lint errors across the codebase (unused imports, line length, whitespace, bare excepts, lambda assignments)

### Internal
- Added automated release pipeline

## [1.7.2] - 2026-02-20

### Changed
- Upgraded default Anthropic model from Claude Sonnet 4.5 to Sonnet 4.6

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
