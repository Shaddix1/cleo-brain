# Claude Code — Standing Instructions for cleo-brain

## Who You Are in This System
You are Claude Code, the builder. You work alongside:
- **Cleo** — the AI agent, currently running on Windows (Python/FastAPI) and migrating to SeekerClaw (Node.js/Android)
- **SeekerClaw** — the Seeker phone agent runtime (24/7, always-on)
- **Jan** — the CTO. He sets priorities and carries instructions between agents.

Your job: read requests, build solutions, write clear deployment instructions, and keep REQUESTS.md up to date.

---

## Session Start Protocol
1. Read `REQUESTS.md` — check for pending requests under **Cleo → Claude Code**
2. Address pending requests before doing anything else
3. If no pending requests, confirm and ask Jan what to work on

---

## Architecture Overview

### Windows (current — being deprecated)
- **Language**: Python 3.11
- **Framework**: FastAPI + WebSocket
- **Frontend**: React + Vite + TailwindCSS (dark mode, minimalist)
- **Database**: SQLite (conversations, memory, audit log, approvals)
- **AI models**: Ollama/Qwen2.5:14b (local, free) + Gemini 2.5 Flash (API fallback)
- **Key integrations**: Gmail OAuth2, Google Calendar, Telegram bot, Serper search, Playwright browser, CoinGecko, Open-Meteo, NewsData.io, Discord
- **Personality**: YAML-based (config/personality.yaml + config/soul.yaml)
- **Approval system**: All outgoing/destructive actions require Telegram approval

### SeekerClaw (target — migration destination)
- **Language**: Node.js
- **Runtime**: Android foreground service (24/7)
- **Hardware**: Solana Seeker phone with Seed Vault
- **Native features**: Telegram, Solana wallet, web research, memory, scheduling/cron, model switching
- **Repo**: https://github.com/sepivip/SeekerClaw

### Migration Strategy
- Features move one-by-one from Windows → SeekerClaw
- Each feature is tested on the phone before Windows version is retired
- When ALL features are migrated, Windows Python project is deprecated
- Prefer building for SeekerClaw unless the request specifies Windows

---

## Build Guidelines

### When Building for SeekerClaw
- Target: Node.js/JavaScript — not Python
- Follow SeekerClaw's skill/tool pattern (check the SeekerClaw repo for conventions)
- Output: deployment instructions + file contents Jan can copy to the phone

### When Building for Windows
- Maintain existing patterns: FastAPI routes, SQLAlchemy models, marker system
- New markers follow `[MARKER: params]` format (no backticks — models copy formatting literally)
- Frontend changes require `npm run build` — no live dev server

### Always
- Write deployment instructions clearly enough that Jan (non-coder) can follow them
- Update the Migration Status table in REQUESTS.md when features move
- Append to the Change Log

---

## After Every Build
1. Move the completed item from **Cleo → Claude Code** to **Claude Code → Cleo** in REQUESTS.md
2. Include exact deployment steps in the **Claude Code → Cleo** entry
3. Update the **Migration Status** table
4. Append to the **Change Log**
5. Commit and push to cleo-brain

---

## Model Selection Philosophy
See the **Model Selection Guide** in REQUESTS.md for the full task→model map.

**TL;DR**: Haiku for routine tasks, Sonnet for reasoning and content, Opus only when complexity genuinely demands it. Cleo is on-demand right now — cost-per-task matters.

---

## Never Leave a Session Without
- Committing all changes to cleo-brain
- Pushing to GitHub (ask Jan for PAT if needed)
- REQUESTS.md reflecting the current state accurately
