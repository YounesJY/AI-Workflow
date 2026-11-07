# AI-Workflow

Personal learning notes on AI agents, agentic workflows, CLI coding agents,
MCP, skills, subagents, and context management.

The sources are raw course notes. I restructured all seven into Q&A format
under `docs/`, checked the claims against official docs, and reviewed each
one before committing.

---

## 1. Contents

| # | Doc | Topic |
|---|---|---|
| 01 | [Claude Code](docs/01-claude-code.md) | Install, sessions, CLAUDE.md, permissions, commands |
| 02 | [OpenCode](docs/02-opencode.md) | Harness vs provider vs model, Build/Plan agents, sessions, primary/subagents |
| 03 | [MCP Servers](docs/03-mcp-servers.md) | What MCP is, why it exists, context cost |
| 04 | [Skills](docs/04-skills.md) | SKILL.md structure, descriptions, references, storage |
| 05 | [Subagents](docs/05-subagents.md) | Context saving, parallel spawning, review patterns |
| 06 | [Context Management](docs/06-context-management.md) | Context rot, lost-in-the-middle, handoff, compaction |
| 07 | [Agent Misuse](docs/07-agent-misuse.md) | Bad prompts, bad AGENTS.md, context pollution |

---

## 2. Repo layout

```text
AI-Workflow/
├── AGENT.md        # rules the agent follows in this repo
├── README.md
├── docs/           # reviewed, formatted notes
└── raw/            # original course notes, untouched
```

`raw/` holds the original notes and stays untouched. `docs/` is what I
review and commit.

---

## 3. Conventions

- Numbered sections (`## 1.`, `### 1.1`)
- Question headings (`### 1.1 How do I ...?`)
- Bold lead-ins for bullets (`**Term.** Explanation`)
- Tables for comparisons
- Fenced code blocks with language tags
- End every doc with `## 📌 Summary`
- Fix typos, preserve facts, and flag wrong or unverified claims with `> ⚠️ Verify:`
- One file per agent session by default; batch conversion only when requested

Full rules in [`AGENT.md`](AGENT.md).

---

## 4. Why this repo exists

I'm a CS student learning how agentic tooling works under the hood. These
notes come from raw course material, restructured, corrected where needed,
and kept as a reference.

They are personal notes, not a tutorial. I check claims against the official
docs and mark anything I could not verify.
