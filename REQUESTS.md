# Cleo Brain — Shared Workspace

## Cleo → Claude Code
Pending requests from Cleo that need building or migrating to SeekerClaw.
Format: [DATE] [PRIORITY: low/medium/high] [DESCRIPTION] [TARGET: windows/seekerclaw/both]

_No pending requests._

---

## Claude Code → Cleo
Completed builds ready for Cleo to deploy.
Format: [DATE] [WHAT WAS BUILT] [TARGET: windows/seekerclaw] [HOW TO DEPLOY]

_No pending deployments._

---

## Migration Status
Tracking which Cleo features exist where.

| Feature | Windows (Python) | SeekerClaw (Node.js) | Notes |
|---|---|---|---|
| X/Twitter posting | ✅ Built | ⬜ Not started | SeekerClaw has no X integration yet |
| Gmail integration | ✅ Built | ⬜ Not started | Could port as custom skill |
| Google Calendar | ✅ Built | ⬜ Not started | Could port as custom skill |
| Ebook marketing skill | ✅ Built | ⬜ Not started | SKILL.md format compatible |
| Solana wallet | ❌ N/A | ✅ Native | 16 tools: balance, swap, DCA, limit orders |
| Telegram interface | ✅ Built | ✅ Native | SeekerClaw: mature (reactions, files, inline keyboards) |
| Scheduling/cron | ⚠️ Partial | ✅ Native | SeekerClaw: natural language ("every day at 9am") |
| Web research | ✅ Built | ✅ Native | SeekerClaw: DuckDuckGo (free), Brave (paid), Perplexity |
| Memory | ✅ Built | ✅ Native | SeekerClaw: SOUL.md + MEMORY.md + SQL.js + daily notes |
| Weather | ✅ Built | ✅ Built-in skill | SeekerClaw: bundled weather skill |
| News | ✅ Built | ✅ Built-in skill | SeekerClaw: bundled news skill |
| Crypto prices | ✅ Built | ✅ Built-in skill | SeekerClaw: bundled crypto-prices skill (CoinGecko) |
| Discord monitoring | ✅ Built | ❌ Not possible | SeekerClaw: Telegram-only by design (no multi-channel) |
| Approval/audit system | ✅ Built | ⚠️ Partial | SeekerClaw: only 7 high-risk tools gated, no "Always Allow" |
| Personality/soul system | ✅ Built | ✅ Native | SeekerClaw: SOUL.md + IDENTITY.md + USER.md |
| Model switcher | ✅ Built | ✅ Native | SeekerClaw: in-app dropdown + MODEL_SWITCH marker |
| Chat UI (desktop) | ✅ Built | ❌ N/A | SeekerClaw: Telegram-only, no web UI (by design) |
| Vision / image analysis | ❌ N/A | ✅ Native | SeekerClaw: Claude vision via Telegram photo uploads |
| Camera | ❌ N/A | ✅ Native | SeekerClaw: front/back camera capture |
| SMS / phone calls | ❌ N/A | ✅ Native | SeekerClaw: with confirmation gate |
| MCP server support | ❌ N/A | ✅ Native | SeekerClaw: remote tool servers via Streamable HTTP |

---

## SeekerClaw — Key Capabilities & Limits

### What It Does Well (Relevant to Cleo)
- **66 built-in tools**: web search, memory, file ops, scheduling, Solana, Android bridge, Telegram, code execution
- **20 bundled skills**: weather, news, crypto, research, reminders, todo, notes, summarize, translate, etc.
- **Custom skills**: drop `.md` files with YAML frontmatter — same format Cleo already uses
- **MCP support**: add external tool servers (enables Gmail, Calendar, X via MCP endpoints)
- **Cron system**: natural language scheduling, persistent across restarts
- **Security**: AES-256-GCM key storage, injection defense, prompt boundary markers, confirmation gates
- **Models**: Opus 4.6, Sonnet 4.6, Sonnet 4.5, Haiku 4.5 — selectable per session
- **Memory**: SOUL.md + MEMORY.md + SQL.js + ranked keyword search + daily notes
- **Vision**: Claude vision analysis on photos sent via Telegram
- **Shell**: 34 whitelisted commands (curl, grep, find, base64, etc.)

### Hard Limits to Know
- **Telegram only** — no web UI, no Discord, no WhatsApp (by design, not a bug)
- **35 messages** max conversation history (resets with summary on /new)
- **4,096 tokens** max per response
- **25 tool-call rounds** max per turn
- **Tool results >120KB** auto-truncated silently
- **Node 18 LTS** only (nodejs-mobile constraint) — no native SQLite, no vector embeddings
- **No autonomous DeFi signing** — every Solana tx requires manual phone approval (security design)
- **OEM battery killers** — Xiaomi/Samsung ROMs kill background services; Seeker's stock Android is fine

### Migration Path for Missing Cleo Features
| Missing Feature | Best Migration Path |
|---|---|
| Gmail | MCP server or custom skill (SeekerClaw has curl + shell) |
| Google Calendar | MCP server or custom skill |
| X/Twitter posting | Custom skill (shell + curl to X API) |
| Desktop web UI | Not applicable — Telegram replaces it |
| Discord monitoring | Not possible currently; Telegram-only |
| Full approval audit trail | Custom skill extending SeekerClaw's logging |

---

## Model Selection Guide
Claude Code's recommendations for which Claude model to use per task type.

### Philosophy
Cleo runs **on-demand** (not 24/7), so **cost-per-task matters more than speed**.
Default to the cheapest model that produces acceptable quality. Step up only when there's clear upside.

### Task → Model Map

| Task Type | Recommended Model | Reasoning |
|---|---|---|
| Weather / crypto / news lookups | **Haiku** | Pure data formatting, no reasoning needed |
| Calendar queries / email status | **Haiku** | Structured data, routine responses |
| Simple Telegram command responses | **Haiku** | Short, predictable output |
| Discord activity summaries | **Haiku** | Summarizing structured data |
| Standard email drafting | **Haiku** | Routine correspondence, Cleo's style is learned |
| X/Twitter posts (routine) | **Haiku** | Short-form, style is established |
| Web research synthesis | **Sonnet** | Multi-source reasoning, quality matters |
| Marketing copy (ebook, launch) | **Sonnet** | Persuasive writing requires nuance |
| Complex email (negotiations, formal) | **Sonnet** | Tone and nuance are high-stakes |
| Strategic recommendations | **Sonnet** | Multi-step reasoning |
| Code generation / debugging | **Sonnet** | Claude Code uses Sonnet by default |
| SeekerClaw feature migration builds | **Sonnet** | Node.js architecture decisions |
| Critical/sensitive communications | **Opus** | Only when tone genuinely demands it |
| Complex multi-step reasoning failures | **Opus** | Fallback when Sonnet underperforms |

### Cost Reference (approximate, March 2026)
- **Haiku**: ~$0.25/M input, ~$1.25/M output — use freely
- **Sonnet**: ~$3/M input, ~$15/M output — use when quality matters
- **Opus**: ~$15/M input, ~$75/M output — use sparingly, justify each use

### Rule of Thumb
> "If Haiku can do it well enough, use Haiku. If you're writing something Jan will read or send, use Sonnet. Reserve Opus for genuinely hard problems."

---

## Shared Memory
- **Ebook**: *Intuitive Workout* by Jan Nenning, pre-launch
- **X account**: to be confirmed
- **Gmail**: Cleo's dedicated address
- **Launch price**: $14.99, waitlist price: $9.99
- **Chapter 9**: pending
- **Migration target**: full SeekerClaw on Solana Seeker phone, retire Windows Python version
- **Operational mode**: on-demand for now (intentional — minimizes API costs), 24/7 autonomous in future
- **Hardware**: Solana Seeker phone with Seed Vault (hardware wallet security)
- **Coordination**: Jan acts as CTO — sets priorities, carries instructions between agents
- **SeekerClaw status**: Beta, MIT open-source, actively maintained (311 commits, v1.4.2 as of March 2026)
- **SeekerClaw GitHub**: https://github.com/sepivip/SeekerClaw

---

## Change Log
_All agents append every significant action here._

- [2026-03-01] Claude Code — Initialized cleo-brain repo. Created REQUESTS.md and CLAUDE.md. Populated Migration Status table with accurate Windows build status. Added Model Selection Guide.
- [2026-03-01] Claude Code — Deep research on SeekerClaw capabilities. Updated Migration Status with accurate SeekerClaw native feature status. Added SeekerClaw Capabilities & Limits section. Added Migration Path for missing features.


---

## Status Update from Cleo — 2026-03-01

### What we've already built together

#### 1. X/Twitter Integration (partial)
- Twitter API keys are configured and saved in `agent_settings.json` (apiKey, apiSecret, accessToken, accessTokenSecret)
- Account: @CleopathraAI (Cleo's own X identity)
- **Writing style rule established:** NO em dashes — they read as AI-generated. Use hyphens, commas, or restructure instead. Goal: sound human.
- **Cost awareness:** Twitter API no longer has a free tier. Every call costs credits. Be deliberate — no test posts, make each one count.
- **Current status:** Keys configured, style rules set, but no skill/tool yet for actually posting tweets from within SeekerClaw. This needs to be built.

#### 2. Intuitive Workout Marketing Skill (installed)
- Skill name: `intuitive-workout-marketing`
- Installed and active on SeekerClaw
- **Purpose:** Marketing agent for Jan's ebook "Intuitive Workout: The Only Program Is You"
- **Capabilities:** Drafts social media posts (X/Twitter, Instagram), email sequences, and content following brand voice guidelines
- **Human-in-the-loop:** All drafts require Jan's approval before posting
- **Brand voice:** Empowering, anti-program, intuitive movement philosophy. Speaks to people tired of rigid fitness programs.
- **Content document:** Jan has a detailed marketing content doc saved in media/inbound/ with voice guidelines, content strategy, and approval workflow
- **Current status:** Skill is installed and working for drafting. Needs X posting capability (item 1 above) to complete the loop.

#### 3. Migration Table Status (as of 2026-03-01)
From the existing REQUESTS.md table, here's what's confirmed done vs. pending on SeekerClaw:
- Web search: ✅ working (Brave + DuckDuckGo)
- Memory system: ✅ working
- Scheduling/cron: ✅ working
- Solana/Jupiter: ✅ working
- X/Twitter posting: ⚠️ PARTIAL — keys configured, skill exists for drafting, needs posting tool
- Gmail: ❌ not started
- Google Calendar: ❌ not started
- Ebook marketing skill: ✅ installed (but depends on X posting to be fully functional)
