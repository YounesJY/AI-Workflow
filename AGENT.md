# AGENT.md — AI-Workflow Docs Repo

## 1. What this repo is

Personal learning notes for AI agents, agentic workflows, CLI agents,
MCP, skills, and subagent patterns. Source material is raw course notes
(messy, typos, fragments). The job: turn each raw note into a clean Q&A
Markdown file under `docs/`.

## 2. MD format (non-negotiable)

- Numbered sections: `## 1.`, `## 2.`, `### 1.1`
- Q&A shape: each `###` heading is a question, followed by a direct answer
- Bold lead-ins for bullets: `**Term.** Explanation`
- Tables for comparisons
- Fenced code blocks with language tags
- `---` between major sections
- End every file with `## 📌 Summary` table or callout
- Preserve source emojis; do not add new ones to technical text
- No raw HTML in the output. Convert `<mark>` and `<u>` to bold text or
  callouts
- Voice: direct, technical, no fluff, no AI-slop words
  (no "delve", "leverage", "robust", "seamless", "it's worth noting")

## 3. Content rules

- Preserve every idea from the source. Fix typos and broken English.
- Verify technical claims against official docs when possible. If a source
  claim is wrong or outdated, keep the original claim in a
  `> ⚠️ Verify:` callout next to the current behavior. Do not silently
  delete it.
- Facts added from official docs must be accurate and should be noted in
  the file intro or a callout.
- Do not invent content. If the source is thin, the MD stays thin; do not
  pad it with generic advice.
- Keep the source's structure of ideas; improve the structure of prose.
- Keep technical terms as-is.

## 4. Workflow

1. Read the raw source file completely before writing anything.
2. Plan the section outline. Post the outline as a message, do not write yet.
3. Wait for approval on the outline.
4. Write the MD to the target path in `docs/`.
5. Self-review pass: check the format rules from section 2, then check
   that no source idea was dropped. Report the diff against source in one
   short list.
6. Do NOT commit. Do NOT push. The user reviews and commits.

**Batch mode.** The default is one file per session. If the user explicitly
asks for a batch, convert all requested files in one pass:

- Read every source file completely before writing.
- Keep the same format and flagging rules for each file.
- Report one combined review list: files written, source ideas dropped,
  claims corrected, and remaining gaps.
- Do not skip the self-review because the batch is large.

## 5. Repo layout

```text
AI-Workflow/
├── AGENT.md
├── README.md
├── docs/
│   ├── 01-claude-code.md
│   ├── 02-opencode.md
│   ├── 03-mcp-servers.md
│   ├── 04-skills.md
│   ├── 05-subagents.md
│   ├── 06-context-management.md
│   ├── 07-agent-misuse.md
│   └── README.md
└── raw/                 # source notes, untouched
```

Mapping:

| Raw source | Target |
|---|---|
| `raw/Claude Code Course.md` | `docs/01-claude-code.md` |
| `raw/open code course.md` | `docs/02-opencode.md` |
| `raw/MCP Servers.md` | `docs/03-mcp-servers.md` |
| `raw/What is SKILL in agents.md` | `docs/04-skills.md` |
| `raw/SubAgnents Workflow.md` | `docs/05-subagents.md` |
| `raw/What is Context.md` | `docs/06-context-management.md` |
| `raw/MisUse Of Agents.md` | `docs/07-agent-misuse.md` |

`raw/README.md` is a repo stub, not a topic doc. Keep it out of the
numbered set.

## 6. Hard rules

- Never touch `raw/`. Read-only.
- Never run `git commit` or `git push`.
- Never rewrite a file marked `REVIEWED` in a `## Status` line.
- One file per session by default. Batch conversion is allowed only when
  the user explicitly requests it.
- If a source file is longer than ~200 lines, spawn an Explore
  subagent to summarize it before writing. In OpenCode, ask the
  primary agent to use the Explore subagent; `@` attaches files, it
  does not spawn agents.
- Delete the old Title Case copy under `docs/` after its replacement is
  written and reviewed. `raw/` stays as the source of truth.

## 7. Commands

- Build/check: none (docs only)
- Format check: manual against section 2
- Optional link check: verify any URL added from official docs

## IMPORTANT NOTES

- All seven topic docs were converted from `raw/` in one batch on
  2026-11-07. Review them as a batch.
- Technical claims were checked against the Claude Code, OpenCode v2,
  and MCP docs. Claims that could not be verified are marked with
  `> ⚠️ Verify:`.
- Keep this file current as the repo evolves.
