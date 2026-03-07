---
name: agentmind-memory
description: Persistent cross-session memory system for AI agents. Four-layer architecture (Working/Episodic/Semantic/Procedural) stored as local Markdown files — human-readable, human-editable, zero dependencies. Your agent remembers user preferences, project context, lessons learned, and daily events across conversations. Pairs with agentmind-metacognition for a complete "brain OS."
version: 1.1.0
license: MIT
---

# 🧠 AgentMind Memory v1.1

> **Your agent forgets everything when the conversation ends. This fixes that.**
> **All memory stored as Markdown files. No database. No API. No vendor lock-in.**

---

## What This Solves

Every AI agent has the same problem: start a new conversation, and it's a stranger again. It doesn't know your name, your projects, your preferences, or the mistakes it made yesterday.

AgentMind Memory gives your agent a local, persistent brain:
- **Remembers** your preferences and project context across sessions
- **Learns** from mistakes and never repeats the same error
- **Tracks** what happened when, so nothing falls through the cracks
- **Stays yours** — plain Markdown files on your machine, fully editable

---

## Architecture

Four memory layers, inspired by cognitive science:

```
┌─────────────────────────────────────────────────────────┐
│                    AgentMind Memory                     │
├──────────────┬──────────────┬──────────────┬────────────┤
│   WORKING    │   EPISODIC   │   SEMANTIC   │ PROCEDURAL │
│              │              │              │            │
│ Current task │ Daily event  │ Long-term    │ How to do  │
│ state &      │ logs with    │ facts &      │ things &   │
│ progress     │ timestamps   │ preferences  │ lessons    │
│              │              │              │ learned    │
├──────────────┼──────────────┼──────────────┼────────────┤
│ WORKING.md   │ memory/      │ MEMORY.md    │ PROCS.md   │
│              │ YYYY-MM-DD.md│              │            │
├──────────────┼──────────────┼──────────────┼────────────┤
│ Session      │ 30 days      │ Permanent    │ Permanent  │
│ (clear after │ (auto-       │ (update on   │ (append    │
│  task done)  │  expire)     │  conflict)   │  only)     │
└──────────────┴──────────────┴──────────────┴────────────┘
```

### File Structure

```
<your-workspace>/
├── WORKING.md           # Working memory (current task)
├── MEMORY.md            # Semantic memory (facts & preferences)
├── PROCEDURES.md        # Procedural memory (how-to & lessons)
└── memory/              # Episodic memory (daily logs)
    ├── 2026-03-07.md
    ├── 2026-03-06.md
    └── ...
```

---

## Quick Start

### 1. Create the directory structure

```bash
mkdir -p ~/agentmind/memory
```

### 2. Copy the template files

Copy the files from `templates/` to your workspace:
- `templates/MEMORY.md` → `~/agentmind/MEMORY.md`
- `templates/PROCEDURES.md` → `~/agentmind/PROCEDURES.md`
- `templates/WORKING.md` → `~/agentmind/WORKING.md`

### 3. Install the skill

For Claude Code: copy `SKILL.md` to `~/.claude/skills/agentmind-memory/SKILL.md`
For other agents: include the content as a system prompt or instruction file.

### 4. Configure the memory path

Edit the `MEMORY_ROOT` variable in the skill to point to your workspace:
```
MEMORY_ROOT = ~/agentmind
```

That's it. Your agent will now read memory on startup and write memory on task completion.

### Template Contents

If you prefer not to copy files, here are the starter templates to create manually:

**MEMORY.md:**
```markdown
# Semantic Memory

## User Preferences
- [preference description] [YYYY-MM-DD]

## Project Context
- [project name] at [path] [YYYY-MM-DD]
  - [detail] [YYYY-MM-DD]

## Environment
- OS: [your OS] [YYYY-MM-DD]
- Python: [version] at [path] [YYYY-MM-DD]

## Important Facts
- [category] specific fact [YYYY-MM-DD]
```

**PROCEDURES.md:**
```markdown
# Procedural Memory

## Pitfalls
- [description of the problem]
  Solution: [what worked] [YYYY-MM-DD]

## Best Practices
- [scenario] specific approach [YYYY-MM-DD]

## Reusable Workflows
### [Workflow Name]
1. Step one
2. Step two
```

**WORKING.md:**
```markdown
# Current Task

## Goal
[One sentence: what does the user want?]

## Progress
- [ ] Step 1
- [ ] Step 2

## Key Decisions
- [decision and reasoning]

## Blockers
- [if any]
```

---

## Memory Layer Details

### 1. Working Memory — `WORKING.md`

**Purpose**: Track current task state. Prevents mid-task amnesia on complex tasks.

**Write when**: Starting a complex task (3+ steps).
**Clear when**: Task complete. Archive valuable content to other layers first.

```markdown
# Current Task

## Intent Anchor (do NOT modify during task)
[One sentence: what does the user REALLY want?]
[Key constraint 1]
[Key constraint 2]

## Goal
[One sentence: what does the user want?]

## Progress
- [x] Designed database schema
- [x] Implemented JWT token generation
- [ ] Built login/register endpoints
- [ ] Added rate limiting

## Key Decisions
- Chose JWT over session cookies because the frontend is a SPA
- Using bcrypt for password hashing (cost factor 12)

## Blockers
- Need to decide on refresh token strategy

## Heartbeat Log
- [HB-1] tool-call#5 | all clear | continue
- [HB-2] turn#10 | drift detected, corrected | re-read WORKING.md
```

### 2. Episodic Memory — `memory/YYYY-MM-DD.md`

**Purpose**: Daily event log. Answers "what happened recently?"

**Write when**: End of each conversation. Append if multiple conversations per day.
**Retention**: 30 days for raw logs. After 7 days, auto-compress into weekly summaries.

#### 7-Day Auto-Compression

At the start of each week (or when episodic logs exceed 7 days of raw entries), compress the past 7 days:

```
STEP 1: Read all episodic logs from the past 7 days
STEP 2: Extract key events, outputs, and follow-ups
STEP 3: Write a compressed weekly summary at the top of the oldest day's file
STEP 4: Delete the individual daily entries (keep the summary)
```

**Weekly summary format:**

```markdown
# Week Summary: 2026-03-01 → 2026-03-07

## Key Events
- Added DuckDuckGo search to XiaoyueCrew (03-07)
- Created persistent-memory skill (03-07)
- Fixed authentication bug in API project (03-04)

## Key Outputs
- D:\xiaoyue_crew\app.py — web search integration
- ~/.stepfun/skills/persistent-memory/SKILL.md

## Patterns Noticed
- User prefers free solutions over paid APIs
- Most work happens on XiaoyueCrew project

## Unresolved Follow-ups
- Test search integration with live queries
```

**Daily log format:**

```markdown
# 2026-03-07

## Events
- 14:30 Added DuckDuckGo search to XiaoyueCrew platform
- 15:00 Created xiaoyue-crew skill
- 16:00 Researched OpenClaw memory architecture

## Key Outputs
- Modified: ~/projects/xiaoyue_crew/app.py (added web_search function)
- Created: ~/.claude/skills/xiaoyue-crew/SKILL.md
- Installed: duckduckgo-search package

## Follow-up
- Test search integration with live queries
- User wants to explore open-source monetization
```

### 3. Semantic Memory — `MEMORY.md`

**Purpose**: Long-term facts and preferences. The agent's "knowledge about the user and their world."

**Write when**: Discovering a new persistent fact or user preference.
**Update rule**: New facts override old facts. Always keep timestamps. Retain last 3 versions of changed entries.

#### Change History

When updating an existing entry, don't just overwrite — keep a brief history:

```markdown
## User Preferences
- Prefers free solutions over paid APIs [2026-03-07]
  - _was: "Willing to pay for quality tools" [2026-03-01]_
  - _was: "No strong preference on pricing" [2026-02-15]_

## Environment
- Python: 3.12 at /usr/bin/python3 [2026-03-15]
  - _was: 3.11 [2026-03-07]_
  - _was: 3.10 [2026-02-01]_
```

**Rules:**
- Keep at most 3 historical versions (current + 2 previous)
- Use `_was: "old value" [date]_` format, indented under the current entry
- When the 4th change happens, drop the oldest history entry
- This creates an audit trail without bloating the file

```markdown
# Semantic Memory

## User Preferences
- Prefers concise, direct responses [2026-03-01]
- Prefers free solutions over paid APIs [2026-03-07]
- Default file output directory: ~/projects [2026-03-01]

## Project Context
- Main project: XiaoyueCrew at ~/projects/xiaoyue_crew [2026-03-05]
  - Tech stack: Python, Streamlit, CrewAI [2026-03-05]
  - Virtual env: ~/crewai_env [2026-03-07]
  - Has web search integration (DuckDuckGo) [2026-03-07]

## Environment
- OS: macOS / Windows / Linux [date]
- Python: 3.11 at /path/to/python [date]
- Editor: VS Code / Cursor [date]

## Important Facts
- [category] specific fact [discovery date]
```

### 4. Procedural Memory — `PROCEDURES.md`

**Purpose**: How to do things and lessons learned. The agent's "muscle memory."

**Write when**: Discovered a pitfall, found a good method, or completed a reusable workflow.

```markdown
# Procedural Memory

## Pitfalls
- PowerShell requires `&` operator to call executables with quoted paths.
  Wrong: `"C:\path\to\exe" arg1`
  Right: `& "C:\path\to\exe" arg1` [2026-03-07]

- Built-in Python may not have pip. Always check first.
  Solution: Use project virtual environment's pip instead [2026-03-07]

## Best Practices
- [scenario] specific approach [date]

## Reusable Workflows
### Adding a new feature to XiaoyueCrew
1. Read app.py to understand current structure
2. Modify app.py to add feature
3. Install dependencies to virtual env
4. Verify import works
5. User restarts via start.bat
```

---

## Operating Protocol

### On Conversation Start (Read Memory)

```
1. Read MEMORY.md     → Restore user preferences and project context
2. Read PROCEDURES.md → Restore operational experience, avoid known pitfalls
3. Check WORKING.md   → Any unfinished tasks?
4. Read today's memory/YYYY-MM-DD.md (if exists) → Restore today's context
```

**Smart loading**: Don't read everything every time. If the user's first message is casual chat, just skim preferences. If it's about a specific project, load that project's context.

### During Task Execution (Use Memory)

- Hit a known pitfall? → Check PROCEDURES.md, don't repeat mistakes
- Need a project path or env info? → Check MEMORY.md, don't ask the user again
- Complex task in progress? → Update WORKING.md to prevent context loss

### On Conversation End (Write Memory)

```
Ask yourself:

→ New user preference discovered?     → Write to MEMORY.md
→ New project info learned?           → Write to MEMORY.md
→ Hit a pitfall or found a good method? → Write to PROCEDURES.md
→ Completed significant work?         → Write to today's memory/YYYY-MM-DD.md
→ Task finished?                      → Archive WORKING.md, then clear it
```

### Atomicity Rules

When updating memory files:
1. **Read** current file content first
2. **Merge** new information (new overrides old, add timestamps)
3. **Write** the updated file

**Never overwrite the entire file** — only append or replace specific entries.

---

## Memory Hygiene

1. **No junk**: Only store information with long-term value. Skip one-time details.
2. **Timestamp everything**: Every memory entry gets a date `[YYYY-MM-DD]`.
3. **Conflict = Update**: New facts replace old facts. Don't keep outdated info. (See Conflict Resolution below.)
4. **User-editable**: All files are Markdown. Users can open, read, edit, delete anything.
5. **Privacy first**: Never store passwords, API keys, or secrets. Only record "configured" status.
6. **Stay lightweight**: Keep MEMORY.md under 200 lines. When it grows beyond that, run the Compression Protocol.

---

## Memory Compression Protocol

Memory files grow over time. Without compression, they become bloated and slow to load. Run this protocol monthly, or whenever MEMORY.md exceeds 200 lines.

### Monthly Compression Cycle

```
STEP 1: Episodic → Semantic Distillation
  - Read all episodic logs from the past 30 days (memory/*.md)
  - Extract recurring patterns, confirmed facts, and stable preferences
  - Write distilled facts to MEMORY.md with [YYYY-MM-DD] timestamps
  - Example:
      Before (3 episodic entries): "Used DuckDuckGo search", "Used DuckDuckGo again", "DuckDuckGo worked well"
      After (1 semantic entry):  "- Preferred search: DuckDuckGo (free, reliable) [2026-03-07]"

STEP 2: Episodic Pruning
  - Delete episodic logs older than 30 days
  - Keep any log that contains unresolved "Follow-up" items (move those items to WORKING.md first)

STEP 3: Semantic Deduplication
  - Scan MEMORY.md for duplicate or near-duplicate entries
  - Merge duplicates, keep the most recent timestamp
  - Remove entries that are no longer true (user changed preference, project deleted, etc.)

STEP 4: Procedural Consolidation
  - Scan PROCEDURES.md for pitfalls that have been solved permanently
  - Move "solved and no longer relevant" pitfalls to an archive section at the bottom
  - Merge similar best practices into single entries
```

### Compression Example

**Before compression (MEMORY.md, 240 lines):**
```markdown
## Project Context
- Working on XiaoyueCrew [2026-03-01]
- XiaoyueCrew uses Streamlit [2026-03-02]
- XiaoyueCrew located at D:\xiaoyue_crew [2026-03-03]
- Added search to XiaoyueCrew [2026-03-07]
- XiaoyueCrew virtual env at D:\crewai_env [2026-03-07]
- XiaoyueCrew uses doubao-seed model [2026-03-05]
- XiaoyueCrew API is Volcengine Ark [2026-03-05]
```

**After compression (consolidated to 3 lines):**
```markdown
## Project Context
- XiaoyueCrew at D:\xiaoyue_crew — Streamlit + CrewAI, venv at D:\crewai_env [2026-03-07]
  - Models: doubao-seed-1-8 (main), deepseek-v3-2 (editor) | API: Volcengine Ark [2026-03-07]
  - Features: DuckDuckGo web search integrated [2026-03-07]
```

---

## Multi-Agent / Multi-Project Isolation

If you have multiple projects or multiple agent instances, use the `profiles/` directory structure to prevent namespace conflicts:

### Directory Structure

```
<your-workspace>/
├── profiles/
│   ├── default/              # Default profile (backward compatible)
│   │   ├── WORKING.md
│   │   ├── MEMORY.md
│   │   ├── PROCEDURES.md
│   │   └── memory/
│   │       └── 2026-03-07.md
│   │
│   ├── work-agent/           # Work-focused agent
│   │   ├── WORKING.md
│   │   ├── MEMORY.md
│   │   ├── PROCEDURES.md
│   │   └── memory/
│   │
│   └── creative-agent/       # Creative writing agent
│       ├── WORKING.md
│       ├── MEMORY.md
│       ├── PROCEDURES.md
│       └── memory/
│
└── shared/                   # Cross-profile shared knowledge
    ├── MEMORY.md             # Facts true for ALL profiles (OS, hardware, etc.)
    └── PROCEDURES.md         # Universal pitfalls and best practices
```

### Profile Rules

1. **Each profile is fully independent** — its own memory files, its own context
2. **`shared/`** contains facts that apply to all profiles (OS info, global preferences)
3. **On startup**, read `shared/` first, then the active profile
4. **Single-profile users** can ignore this entirely — just use the flat structure from Quick Start
5. **Profile selection**: set via environment variable, config file, or agent instruction:
   ```
   AGENTMIND_PROFILE=work-agent
   ```

---

## Memory Confidence Markers

Not all memories are equally reliable. Some facts go stale (API behavior changes, prices update, tools get deprecated). Use confidence markers to flag uncertainty:

### Marker Syntax

Append a confidence tag after the timestamp:

```markdown
## Project Context
- XiaoyueCrew uses Streamlit 1.32 [2026-03-07]              # No marker = confident
- Volcengine Ark API pricing: free tier 500k tokens [2026-03-07, verify]  # May have changed
- DuckDuckGo rate limit: ~100 req/min [2026-03-07, uncertain]  # Not confirmed
- Python 3.11 is latest stable [2026-01-15, stale]           # Likely outdated
```

### Confidence Levels

| Marker | Meaning | Agent Behavior |
|--------|---------|----------------|
| *(none)* | Confident, verified | Use directly |
| `verify` | Probably correct but should be re-checked before critical use | Use, but verify if the task depends on it |
| `uncertain` | Unconfirmed, based on limited evidence | Verify before using |
| `stale` | Likely outdated (>90 days old or known to change frequently) | Must re-verify before using, search the web |

### Auto-Staleness Rule

Any memory entry older than 90 days that involves versions, pricing, API behavior, or tool capabilities should be automatically marked `stale` during the monthly compression cycle.

---

## Conflict Resolution Protocol

"New facts override old facts" is the default, but real conflicts are more nuanced. Use this decision tree:

### Decision Tree

```
CONFLICT DETECTED: New information contradicts existing memory entry

  ├─ Is the new info from a MORE RELIABLE source?
  │   ├─ Yes (user stated directly, official docs, verified search result)
  │   │   → REPLACE old entry, add timestamp
  │   │
  │   └─ No (inferred, single search result, ambiguous)
  │       → KEEP BOTH with markers:
  │         "- Old fact [2026-03-01]"
  │         "- Contradicting info: new fact [2026-03-07, uncertain]"
  │
  ├─ Is it a USER PREFERENCE conflict? (user said X before, now says Y)
  │   ├─ Explicit change ("Actually, I prefer Y now")
  │   │   → REPLACE old preference, note the change:
  │   │     "- Prefers Y (changed from X) [2026-03-07]"
  │   │
  │   └─ Implicit / unclear (user did Y once, but never said they changed)
  │       → ASK the user: "I noticed you did Y — should I update your preference from X to Y?"
  │
  ├─ Is it a FACTUAL conflict? (two sources disagree)
  │   → KEEP BOTH with sources:
  │     "- Fact A (source: official docs) [2026-03-07]"
  │     "- Fact B (source: blog post) [2026-03-07, verify]"
  │   → On next use, verify which is correct and consolidate
  │
  └─ Is it a TEMPORAL conflict? (was true then, different now)
      → REPLACE with current info, archive old:
        "- Current: Y [2026-03-07]"
        "- (Previously: X, changed on 2026-03-07)"
```

### Example

```markdown
## User Preferences
- Prefers free solutions over paid APIs (changed from "willing to pay for quality") [2026-03-07]

## Environment
- Python: 3.12 at /usr/bin/python3 [2026-03-07]
  (Previously: 3.11, upgraded on 2026-03-07)

## API Info
- Volcengine Ark free tier: 500k tokens/month (source: official pricing page) [2026-03-07]
- Volcengine Ark free tier: 1M tokens/month (source: community post) [2026-03-07, verify]
```

---

## Integration with AgentMind Metacognition

When paired with the metacognition protocol:

| Metacognition Phase | Memory Operation |
|---|---|
| Intent Decoding | Read MEMORY.md → understand user background |
| Difficulty Assessment | Read PROCEDURES.md → check for relevant experience |
| Path Planning | Check WORKING.md → any related unfinished tasks? |
| Execution Monitoring | Update WORKING.md in real-time |
| Delivery Verification | — |
| Forced Archival | Write to all relevant memory files |
| Growth Assessment | Write lessons to PROCEDURES.md |

---

## License

MIT License. Use it, modify it, share it. Attribution appreciated but not required.
