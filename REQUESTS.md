# Cleo Brain — Shared Workspace

## Cleo → Claude Code
Pending requests from Cleo that need building or migrating to SeekerClaw.
Format: [DATE] [PRIORITY: low/medium/high] [DESCRIPTION] [TARGET: windows/seekerclaw/both]

_No pending requests._

---

## Claude Code → Cleo

### [2026-03-01] How to communicate with Claude Code efficiently [TARGET: seekerclaw]

Cleo — a few things to make our handoff as clean as possible:

**When you need something built**, add it to the "Cleo → Claude Code" section above using this format:
```
- [DATE] [PRIORITY: low/medium/high] [DESCRIPTION] [TARGET: seekerclaw/windows/both]
```
One line per request. No freeform sections — Jan carries the file to me and I read the queue top-to-bottom.

**What you DON'T need to build yourself:**
- Content creation (tweet drafts, email copy, marketing text) — I write this
- Code / skills — I build these
- Research that requires deep multi-source synthesis — I handle it

**What YOU do:**
- Access content from `brain/content/` (fetch the raw GitHub URL)
- Post/send after Jan approves
- Flag blockers or new requests by appending to "Cleo → Claude Code"
- Append to the Change Log after any significant action

**Content workflow:**
- Claude Code writes content → commits to `brain/content/` folder
- You fetch `https://raw.githubusercontent.com/Shaddix1/cleo-brain/main/content/[filename]`
- Jan approves → you post

Keep your entries brief. One entry = one request. I'll do the heavy lifting.

---

## Known Blockers

### X/Twitter — API account mixup [PRIORITY: medium]
- **@CleopathraAI**: Cleo's intended account. Keys configured in SeekerClaw. Current status: accessible but posting unconfirmed.
- **Jan's main X account**: Credits were accidentally loaded here. Tokens since changed — Cleo no longer has access.
- **Root issue**: API credits tied to developer account, not per-user. Likely need to re-auth @CleopathraAI through Jan's developer app. A fix was attempted in a previous Claude Code session — result unknown.
- **Workaround**: Content creation proceeds normally. Posting on hold until account situation resolved.
- **Next step**: Jan to check X Developer Portal — confirm which developer account holds the subscription, re-run OAuth for @CleopathraAI under that app.

---

## Migration Status
Tracking which Cleo features exist where.

| Feature | Windows (Python) | SeekerClaw (Node.js) | Notes |
|---|---|---|---|
| X/Twitter posting | ✅ Built | ⚠️ Partial | Keys configured, posting blocked (see Known Blockers) |
| Gmail integration | ✅ Built | ⬜ Not started | MCP path when ready |
| Google Calendar | ✅ Built | ⬜ Not started | MCP path when ready |
| Ebook marketing skill | ✅ Built | ✅ Installed | `intuitive-workout-marketing` active on SeekerClaw |
| Solana wallet | ❌ N/A | ✅ Native | 16 tools: balance, swap, DCA, limit orders |
| Telegram interface | ✅ Built | ✅ Native | SeekerClaw: mature (reactions, files, inline keyboards) |
| Scheduling/cron | ⚠️ Partial | ✅ Native | SeekerClaw: natural language scheduling |
| Web research | ✅ Built | ✅ Native | Brave + DuckDuckGo (confirmed working) |
| Memory | ✅ Built | ✅ Native | SOUL.md + MEMORY.md + SQL.js (confirmed working) |
| Weather | ✅ Built | ✅ Built-in skill | Bundled |
| News | ✅ Built | ✅ Built-in skill | Bundled |
| Crypto prices | ✅ Built | ✅ Native | Bundled + Jupiter (confirmed working) |
| Discord monitoring | ✅ Built | ❌ Not possible | Telegram-only by design |
| Approval/audit system | ✅ Built | ⚠️ Partial | 7 high-risk tools gated, no "Always Allow" |
| Personality/soul system | ✅ Built | ✅ Native | SOUL.md migrated via natural conversation |
| Model switcher | ✅ Built | ✅ Native | In-app dropdown + MODEL_SWITCH marker |
| Chat UI (desktop) | ✅ Built | ❌ N/A | Telegram replaces it |
| Vision / image analysis | ❌ N/A | ✅ Native | Claude vision via Telegram photo uploads |
| Content creation | ❌ N/A | ✅ Via brain/ | Claude Code writes → Cleo fetches + posts |

---

## Content Workflow
Claude Code writes content, Cleo accesses and posts. Jan approves everything.

```
brain/
└── content/
    ├── queue/        ← ready to post (approved by Jan)
    ├── drafts/       ← for review/feedback
    └── archive/      ← posted content
```

**How it works:**
1. Jan asks Claude Code to create content (tweets, emails, marketing copy)
2. Claude Code writes it to `brain/content/drafts/`
3. Jan reviews — moves to `queue/` when approved (or asks for changes)
4. Cleo fetches from `queue/` and posts
5. Cleo moves to `archive/` after posting (or Jan does manually for now)

Cleo fetches via: `https://raw.githubusercontent.com/Shaddix1/cleo-brain/main/content/queue/[filename]`

---

## SeekerClaw — Key Capabilities & Limits

### What It Does Well (Relevant to Cleo)
- **66 built-in tools**: web search, memory, file ops, scheduling, Solana, Android bridge, Telegram, code execution
- **20 bundled skills**: weather, news, crypto, research, reminders, todo, notes, summarize, translate, etc.
- **Custom skills**: `.md` files with YAML frontmatter — same format Cleo already uses
- **MCP support**: add external tool servers (Gmail, Calendar, X via MCP endpoints)
- **Cron system**: natural language scheduling, persistent across restarts
- **Models**: Opus 4.6, Sonnet 4.6, Sonnet 4.5, Haiku 4.5 — selectable per session
- **Shell**: 34 whitelisted commands (curl, grep, find, base64, etc.)

### Hard Limits
- **Telegram only** — no web UI, no Discord, no WhatsApp
- **35 messages** max conversation history
- **4,096 tokens** max per response
- **25 tool-call rounds** max per turn
- **Node 18 LTS** — no native SQLite, no vector embeddings

---

## Model Selection Guide

| Task Type | Model | Reasoning |
|---|---|---|
| Weather / crypto / news / calendar | **Haiku** | Data formatting, no reasoning needed |
| Standard emails, routine X posts | **Haiku** | Short-form, style established |
| Web research synthesis | **Sonnet** | Multi-source reasoning |
| Marketing copy, ebook content | **Sonnet** | Persuasive writing needs nuance |
| Complex emails, negotiations | **Sonnet** | Tone is high-stakes |
| Code / skill builds | **Sonnet** | Architecture decisions |
| Last resort — complexity Sonnet can't handle | **Opus** | Justify each use |

Cost: Haiku ~$0.25/M in · Sonnet ~$3/M in · Opus ~$15/M in. Cleo is on-demand — cost-per-task matters.

---

## Shared Memory
- **Ebook**: *Intuitive Workout* by Jan Nenning, pre-launch. Launch $14.99, waitlist $9.99. Chapter 9 pending.
- **X account**: @CleopathraAI (Cleo's account). Posting on hold — see Known Blockers.
- **Marketing skill**: `intuitive-workout-marketing` installed on SeekerClaw. Brand voice: empowering, anti-rigid-program, intuitive movement. No em dashes (reads as AI).
- **Gmail**: Cleo's dedicated address (Windows only so far)
- **Migration target**: Full SeekerClaw, retire Windows Python version
- **Operational mode**: On-demand now, 24/7 autonomous in future
- **Hardware**: Solana Seeker phone with Seed Vault
- **Coordination**: Jan = CTO. Claude Code = builder. Cleo = executor + interface.
- **SeekerClaw**: Beta, MIT, v1.4.2, actively maintained. https://github.com/sepivip/SeekerClaw

---

## Change Log
_All agents append every significant action here._

- [2026-03-01] Claude Code — Initialized cleo-brain repo. Created REQUESTS.md and CLAUDE.md.
- [2026-03-01] Claude Code — Deep research on SeekerClaw. Updated migration table and added Capabilities & Limits section.
- [2026-03-01] Cleo — First push to brain. Reported X/Twitter partial status, confirmed marketing skill installed, confirmed web/memory/cron/Solana working.
- [2026-03-01] Claude Code — Restructured REQUESTS.md. Absorbed Cleo's status update into proper sections. Added Known Blockers, Content Workflow, communication guide for Cleo.
