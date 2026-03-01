# Claude Code — Standing Instructions for cleo-brain

## The System
- **Claude Code** (you) — builder. Reads requests, writes solutions, writes deployment steps.
- **Cleo** — AI agent on Windows (Python/FastAPI), being migrated to SeekerClaw.
- **SeekerClaw** — Node.js AI agent running 24/7 on Jan's Solana Seeker phone.
- **Jan** — CTO. Sets priorities, carries instructions between agents (non-coder).

---

## Session Start
1. Read `REQUESTS.md` → check **Cleo → Claude Code** for pending requests
2. Address those first
3. If none, ask Jan what to work on

---

## Platform Reference

### Windows (being deprecated)
- Python 3.11 / FastAPI / SQLAlchemy / React+Vite frontend
- Models: Qwen2.5:14b (Ollama, local) + Gemini 2.5 Flash (API fallback)
- Marker system: Cleo outputs `[MARKER: params]` → `browse_parser.py` routes to handler
- **No backticks around markers** — models copy formatting literally, breaking regex parsing
- Frontend changes need `npm run build` (no dev server)

### SeekerClaw (migration target)
- Node.js 18 LTS / CommonJS / SQL.js (WASM SQLite — no native bindings)
- Interface: **Telegram only** (no web UI, no Discord — by design)
- 66 built-in tools, 20 bundled skills (weather, news, crypto, cron, memory, Solana, etc.)
- **Hard limits**: 35 msg history · 4,096 tokens/response · 25 tool rounds/turn · 120KB tool result truncation
- **Custom skills**: YAML frontmatter `.md` files — same format Cleo already uses
- **Deployment**: write the `.md` skill file → tell Jan to upload it via Telegram to SeekerClaw, or install from URL with `skill_install`
- **Missing integrations** (Gmail, Calendar, X): migrate via **MCP server** (SeekerClaw supports Streamable HTTP tool servers)
- **No vector embeddings** — keyword search only (WASM limitation on Node 18)
- Repo: https://github.com/sepivip/SeekerClaw

---

## Build Guidelines

**Default target: SeekerClaw** unless request specifies Windows.

| Building for | Key rules |
|---|---|
| **SeekerClaw** | Write as `.md` skill (YAML frontmatter + instructions). For integrations needing API calls, use shell tool (curl) or propose an MCP server. Node 18 — no npm packages with native bindings. |
| **Windows** | Maintain FastAPI/SQLAlchemy patterns. New markers: `[MARKER: params]`, no backticks. Frontend: `npm run build` after changes. |
| **Both** | Write deployment steps Jan can follow without coding knowledge. |

---

## After Every Build
1. Move item: **Cleo → Claude Code** → **Claude Code → Cleo** in REQUESTS.md
2. Include step-by-step deployment instructions in that entry
3. Update the **Migration Status** table
4. Append to the **Change Log**
5. Commit + push to cleo-brain

---

## Model Selection
Full guide in REQUESTS.md. Short version:
- **Haiku** — routine lookups, standard emails, short posts
- **Sonnet** — marketing copy, web research, complex emails, builds
- **Opus** — last resort only

Cleo is on-demand, not 24/7. Cost-per-task matters more than speed.

---

## Never Leave a Session Without
- All changes committed and pushed to cleo-brain
- REQUESTS.md accurately reflecting current state
