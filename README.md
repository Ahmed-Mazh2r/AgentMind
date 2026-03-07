<div align="center">

# 🧠 AgentMind

### A Brain Operating System for AI Agents

**Metacognition (how to think) + Persistent Memory (what to remember)**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](http://makeapullrequest.com)
[![Email](https://img.shields.io/badge/Email-Contact-blue?logo=gmail&logoColor=white)](mailto:769811481@qq.com)

[Quick Start](#-quick-start) · [Why AgentMind](#-why-agentmind) · [Architecture](#-architecture) · [Examples](#-examples) · [Contributing](#-contributing)

</div>

---

## The Problem

Every AI agent has two fatal flaws:

1. **It doesn't think about HOW it thinks.** It jumps to answers, fabricates data, forgets the original goal mid-task, and repeats the same failing approach.

2. **It forgets EVERYTHING when the conversation ends.** New chat = total stranger. Your preferences, your projects, yesterday's progress — all gone.

AgentMind fixes both.

---

## 💡 Why AgentMind

| | Without AgentMind | With AgentMind |
|---|---|---|
| **New conversation** | "What project? What language? Where is it?" | Reads your context from memory files, starts working immediately |
| **Known pitfall** | Wastes 10 minutes rediscovering the same fix | Checks procedural memory, avoids it in 2 seconds |
| **Research task** | Generates plausible-sounding fabricated statistics | Searches the web first, cites real sources |
| **Complex task** | Forgets the goal halfway through | Re-checks original intent after every major step |
| **Same error 3x** | Tries the same approach a 4th time | Hard rule: switches to a completely different method |

**No database. No API keys. No vendor lock-in.** Just Markdown files on your machine.

---

## 🚀 Quick Start

### Option 1: Full Install (Metacognition + Memory)

```bash
# Clone the repo
git clone https://github.com/q7766206/AgentMind.git

# For Claude Code
cp -r AgentMind/metacognition ~/.claude/skills/agentmind-metacognition
cp -r AgentMind/memory ~/.claude/skills/agentmind-memory

# Create your memory workspace
mkdir -p ~/agentmind/memory
cp AgentMind/memory/templates/* ~/agentmind/
```

### Option 2: Metacognition Only

Just want better thinking without persistent memory?

```bash
cp -r AgentMind/metacognition ~/.claude/skills/agentmind-metacognition
```

### Option 3: Memory Only

Just want persistent memory without the thinking protocol?

```bash
cp -r AgentMind/memory ~/.claude/skills/agentmind-memory
mkdir -p ~/agentmind/memory
cp AgentMind/memory/templates/* ~/agentmind/
```

### For Other AI Agents (GPT, Gemini, etc.)

Include the content of `metacognition/SKILL.md` and/or `memory/SKILL.md` in your system prompt or instruction file.

### Platform-Specific Integration

<details>
<summary><b>Claude Code</b></summary>

```bash
# Copy skill files to Claude's skill directory
cp -r AgentMind/metacognition ~/.claude/skills/agentmind-metacognition
cp -r AgentMind/memory ~/.claude/skills/agentmind-memory

# Create memory workspace
mkdir -p ~/agentmind/memory
cp AgentMind/memory/templates/* ~/agentmind/
```

Claude Code will auto-detect skills in `~/.claude/skills/`.
</details>

<details>
<summary><b>Cursor</b></summary>

```bash
# Copy to Cursor's rules directory
mkdir -p .cursor/rules
cp AgentMind/metacognition/SKILL.md .cursor/rules/agentmind-metacognition.md
cp AgentMind/memory/SKILL.md .cursor/rules/agentmind-memory.md

# Create memory workspace in your project root
mkdir -p .agentmind/memory
cp AgentMind/memory/templates/* .agentmind/
```

Then in Cursor Settings → Rules, the files will be auto-loaded as project rules.
</details>

<details>
<summary><b>Windsurf</b></summary>

```bash
# Copy to Windsurf's rules directory
mkdir -p .windsurf/rules
cp AgentMind/metacognition/SKILL.md .windsurf/rules/agentmind-metacognition.md
cp AgentMind/memory/SKILL.md .windsurf/rules/agentmind-memory.md

# Create memory workspace
mkdir -p .agentmind/memory
cp AgentMind/memory/templates/* .agentmind/
```
</details>

<details>
<summary><b>Cline (VS Code)</b></summary>

```bash
# Copy to Cline's custom instructions directory
mkdir -p .cline/rules
cp AgentMind/metacognition/SKILL.md .cline/rules/agentmind-metacognition.md
cp AgentMind/memory/SKILL.md .cline/rules/agentmind-memory.md

# Create memory workspace
mkdir -p .agentmind/memory
cp AgentMind/memory/templates/* .agentmind/
```

Or paste the SKILL.md content into Cline's "Custom Instructions" field in settings.
</details>

<details>
<summary><b>ChatGPT / GPT-4 (Custom Instructions)</b></summary>

1. Open ChatGPT → Settings → Personalization → Custom Instructions
2. Paste the content of `metacognition/SKILL.md` into "How would you like ChatGPT to respond?"
3. For memory: ChatGPT has built-in memory, but you can paste `memory/SKILL.md` to enhance its memory protocol

Note: ChatGPT's custom instructions have a character limit (~1500 chars). Use the condensed version from `examples/chatgpt-condensed.md` (coming soon).
</details>

<details>
<summary><b>Any LLM via System Prompt</b></summary>

For any LLM API (OpenAI, Anthropic, Google, local models):

```python
# Python example
with open("AgentMind/metacognition/SKILL.md") as f:
    metacognition = f.read()

with open("AgentMind/memory/SKILL.md") as f:
    memory = f.read()

system_prompt = f"""
{metacognition}

---

{memory}
"""

# Use as system message in your API call
response = client.chat.completions.create(
    model="your-model",
    messages=[
        {"role": "system", "content": system_prompt},
        {"role": "user", "content": user_message}
    ]
)
```
</details>

---

## 🏗 Architecture

```
AgentMind
├── metacognition/          # HOW your agent thinks
│   └── SKILL.md            # The thinking protocol
│
├── memory/                 # WHAT your agent remembers
│   ├── SKILL.md            # The memory protocol
│   └── templates/          # Starter templates
│       ├── MEMORY.md       # Semantic memory (facts & preferences)
│       ├── PROCEDURES.md   # Procedural memory (lessons learned)
│       └── WORKING.md      # Working memory (current task)
│
└── examples/               # Real-world usage examples
    └── usage-examples.md
```

### Metacognition Protocol

The thinking OS. It makes your agent:

- **Decode intent** — understand what the user *really* wants (not just what they said)
- **Think in 3 layers** — surface problem → real need → next problem
- **Validate in reverse** — actively try to disprove its own conclusions
- **Monitor itself** — catch fake completion, vague filler, mid-task amnesia
- **Fail-3-Switch** — same method fails 3 times? Mandatory method change
- **Search offensively** — web search is a primary weapon, not a last resort

### Memory System

Four layers of persistent memory, inspired by cognitive science:

| Layer | File | Purpose | Lifespan |
|-------|------|---------|----------|
| **Working** | `WORKING.md` | Current task state & progress | Session (cleared after task) |
| **Episodic** | `memory/YYYY-MM-DD.md` | Daily event logs | 30 days |
| **Semantic** | `MEMORY.md` | Long-term facts & user preferences | Permanent |
| **Procedural** | `PROCEDURES.md` | How-to knowledge & lessons learned | Permanent |

All stored as **plain Markdown**. Open them in any text editor. Edit them. Delete what you don't want remembered. You're in full control.

### Advanced Features

- **Memory Compression** — Monthly protocol to distill episodic logs into semantic facts, deduplicate, and keep files lean
- **Confidence Markers** — Tag memories as `verify`, `uncertain`, or `stale` so the agent knows what to re-check
- **Conflict Resolution** — Decision tree for handling contradictory information (preference changes, source disagreements, temporal updates)
- **Multi-Profile Support** — `profiles/` directory structure for multiple agents or projects with shared knowledge base

---

## 📖 Examples

### Agent Remembers Your Setup

```
Day 1: "My project is at ~/projects/myapp, using Python 3.11 with FastAPI."
       → Agent writes to MEMORY.md

Day 2: "Add a new endpoint."
       → Agent reads MEMORY.md, knows the project, framework, and location
       → Starts working immediately. No questions asked.
```

### Agent Learns From Mistakes

```
Session 1: Agent tries `pip install` on system Python. Fails (no pip).
           Discovers workaround: use venv pip.
           → Writes to PROCEDURES.md

Session 2: Same situation arises.
           → Agent reads PROCEDURES.md, uses venv pip directly.
           → Zero time wasted.
```

### Metacognition Prevents Fabrication

```
User: "Write a market analysis report."

Without metacognition:
  → Agent generates 2000 words of plausible but fabricated statistics

With metacognition:
  → Agent assesses: "Information completeness: 30%. I need real data."
  → Searches the web, finds actual statistics
  → Writes report with cited sources
```

See more in [`examples/usage-examples.md`](examples/usage-examples.md).

---

## 🔗 How It Compares

| Feature | AgentMind | Mem0 | MemOS | OpenClaw Memory |
|---------|-----------|------|-------|-----------------|
| **Storage** | Local Markdown | Vector DB + Graph DB | Vector DB + API | Local Markdown |
| **Dependencies** | None | Python SDK + API key | Python SDK + API key | Part of larger framework |
| **Human-readable** | ✅ Plain text | ❌ Embeddings | ❌ Embeddings | ✅ Plain text |
| **Human-editable** | ✅ Any text editor | ❌ API only | ❌ API only | ✅ Any text editor |
| **Metacognition** | ✅ Built-in | ❌ | ❌ | ❌ |
| **Memory compression** | ✅ Monthly protocol | ✅ Auto | ✅ Auto | ❌ |
| **Confidence markers** | ✅ verify/uncertain/stale | ❌ | ❌ | ❌ |
| **Multi-profile** | ✅ profiles/ directory | ✅ User-scoped | ✅ User-scoped | ❌ |
| **Conflict resolution** | ✅ Decision tree | ❌ Last-write-wins | ❌ | ❌ |
| **Zero-code setup** | ✅ Copy files | ❌ Requires coding | ❌ Requires coding | ❌ Requires setup |
| **Cross-agent** | ✅ Claude/Cursor/Windsurf/GPT/any LLM | ❌ SDK only | ❌ SDK only | ❌ OpenClaw only |
| **Cost** | Free | Free tier + paid | Free tier + paid | Free |
| **Works offline** | ✅ | ❌ | ❌ | ✅ |

**AgentMind's niche**: Zero-dependency, human-readable, works-offline memory + thinking protocol. Not competing with Mem0/MemOS on vector search — competing on simplicity and transparency.

---

## 💬 Contact

📧 **Email**: [769811481@qq.com](mailto:769811481@qq.com) — Questions, collaborations, feedback welcome.

---

## 🤝 Contributing

Contributions welcome! Here's how:

1. **Fork** the repo
2. **Create** a feature branch (`git checkout -b feature/amazing-feature`)
3. **Commit** your changes (`git commit -m 'Add amazing feature'`)
4. **Push** to the branch (`git push origin feature/amazing-feature`)
5. **Open** a Pull Request

### Ideas for Contributions

- 🌍 **Translations** — translate SKILL.md files to other languages (Chinese, Japanese, Korean, Spanish...)
- 🧪 **More examples** — real-world usage scenarios
- 🔌 **Integrations** — adapters for other AI platforms (Cursor, Windsurf, Cline...)
- 📊 **Memory analytics** — scripts to visualize memory growth over time
- 🔄 **Sync** — optional cloud sync for multi-device setups

---

## 📄 License

MIT License. See [LICENSE](LICENSE) for details.

Use it, modify it, share it. Attribution appreciated but not required.

---

<div align="center">

**Built with ❤️ for the AI agent community**

*If AgentMind helps you, consider giving it a ⭐ on GitHub!*

📧 [769811481@qq.com](mailto:769811481@qq.com)

</div>
