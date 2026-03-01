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
| X/Twitter posting | ✅ Built | ⬜ Not started | OAuth skill exists in SeekerClaw |
| Gmail integration | ✅ Built | ⬜ Not started | Read, send, reply, draft, trash, star |
| Google Calendar | ✅ Built | ⬜ Not started | View + create events, approval gated |
| Ebook marketing skill | ✅ Built | ⬜ Not started | SKILL.md ready |
| Solana wallet | ❌ N/A | ✅ Native | SeekerClaw + Seed Vault hardware |
| Telegram interface | ✅ Built | ✅ Native | SeekerClaw built-in |
| Scheduling/cron | ⚠️ Partial | ✅ Native | Windows: daily tasks on startup only |
| Web research | ✅ Built | ✅ Native | Windows: Serper API + Playwright |
| Memory | ✅ Built | ✅ Native | Windows: SQLite with keyword search |
| Weather | ✅ Built | ⬜ Not started | Open-Meteo, no API key needed |
| News | ✅ Built | ⬜ Not started | NewsData.io, 200 credits/day |
| Crypto prices | ✅ Built | ✅ Native | Windows: CoinGecko; SeekerClaw: native |
| Discord monitoring | ✅ Built | ⬜ Not started | On-demand channel reading |
| Approval/audit system | ✅ Built | ⬜ Not started | Telegram inline buttons |
| Personality/soul system | ✅ Built | ✅ Migrated | config/personality.yaml + soul.yaml |
| Model switcher | ✅ Built | ✅ Native | Windows: Qwen/Gemini; SeekerClaw: native |
| Chat UI (desktop) | ✅ Built | ❌ N/A | React frontend — not needed on phone |

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

---

## Change Log
_All agents append every significant action here._

- [2026-03-01] Claude Code — Initialized cleo-brain repo. Created REQUESTS.md and CLAUDE.md. Populated Migration Status table with accurate Windows build status. Added Model Selection Guide.
