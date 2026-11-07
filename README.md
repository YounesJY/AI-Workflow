# AI-Workflow

Personal learning notes on AI agents, agentic workflows, CLI coding agents,
MCP, skills, subagents, and context management.

Source material is raw course notes. Everything in `docs/` has been
reformatted into Q&A structure and reviewed manually before commit.

---

## 1. Contents

| # | Doc | Topic |
|---|---|---|
| 01 | [Claude Code](docs/01-claude-code.md) | Install, sessions, CLAUDE.md, permissions, commands |
| 02 | [OpenCode](docs/02-opencode.md) | Harness vs provider vs model, plan/build modes, sessions, primary/subagents |
| 03 | [MCP Servers](docs/03-mcp-servers.md) | What MCP is, why it exists, context cost |
| 04 | [Skills](docs/04-skills.md) | SKILL.md structure, descriptions, references, storage |
| 05 | [Subagents](docs/05-subagents.md) | Context saving, parallel spawning, review patterns |
| 06 | [Context Management](docs/06-context-management.md) | Context rot, lost-in-the-middle, handoff, compaction |
| 07 | [Agent Misuse](docs/07-agent-misuse.md) | Bad prompts, bad AGENTS.md, context pollution |

---

## 2. Repo layout

AI-Workflow/
├── AGENT.md        # rules the agent follows in this repo
├── README.md
├── docs/           # reviewed, formatted notes
└── raw/            # original course notes, untouched

`raw/` is read-only source. `docs/` is what gets reviewed and committed.

---

## 3. Conventions

- Numbered sections (`## 1.`, `### 1.1`)
- Bold lead-ins for bullets (`**Term.** Explanation`)
- Tables for comparisons
- End every doc with `## 📌 Summary`
- Typos fixed, facts preserved, wrong claims flagged with `> ⚠️ Verify:`
- One file per agent session — context rot is real

Full rules in [`AGENT.md`](AGENT.md).

---

## 4. Status

| Doc | Status |
|---|---|
| 01 Claude Code | 🟡 In progress |
| 02 OpenCode | ⬜ Pending |
| 03 MCP Servers | ⬜ Pending |
| 04 Skills | ⬜ Pending |
| 05 Subagents | ⬜ Pending |
| 06 Context Management | ⬜ Pending |
| 07 Agent Misuse | ⬜ Pending |

Legend: ⬜ pending · 🟡 in progress · ✅ reviewed and committed

---

## 5. Why this repo exists

I'm a CS student learning how agentic tooling actually works under the hood —
not just "run this command." These notes are the output of that process:
raw course material, restructured, corrected where needed, kept as reference.

Not a tutorial. Not authoritative. Just my notes, kept honest.
