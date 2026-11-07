# Skills

## 1. What Is a Skill?

A skill is a reusable instruction package for a specific task. It is a long prompt that the agent loads only when needed, so it saves context compared with pasting the same instructions into every conversation.

The source's definition is close: "a long guidance prompt that saves you a lot of tokens and gives better results." The important part is the loading model:

- The agent sees only the skill's **name and description** during normal work.
- The full `SKILL.md` body loads when the agent invokes the skill.
- Supporting files load only when the skill tells the agent to read them.

That is progressive disclosure: pay for the description up front, pay for the body on demand.

---

## 2. Structure

A skill is a folder with a mandatory `SKILL.md` and optional supporting files.

```text
.claude/skills/release/
├── SKILL.md
├── references/
│   └── release-policy.md
├── scripts/
│   └── changelog.ts
└── assets/
    └── template.md
```

```markdown
---
name: git-release
description: Prepare release notes, version bumps, and a GitHub release. Use when the user asks to cut a release.
---

## Workflow

1. Read `references/release-policy.md`.
2. Summarize merged changes since the previous tag.
3. Propose the version bump before changing files.
4. Run `scripts/changelog.ts` only after the user approves.
```

`SKILL.md` is mandatory in the directory form. OpenCode also accepts a flat Markdown file at the skills root, such as `skills/review.md`.

The source says "the SKILL.md metadata section is the most important part." The description is certainly the most important field, because it decides whether the skill is ever loaded.

---

## 3. Frontmatter and Description

### 3.1 Which frontmatter fields matter?

| Field                      | Purpose                                                |
| -------------------------- | ------------------------------------------------------ |
| `name`                     | Display name; in Claude Code, the command name         |
| `description`              | What the skill does and when to use it                 |
| `allowed-tools`            | Claude Code: tools the skill may use without asking    |
| `model`                    | Claude Code: model override while the skill is active  |
| `disable-model-invocation` | Prevent automatic invocation; run only by name         |
| `context: fork`            | Claude Code: run the skill in a forked subagent        |
| `paths`                    | Claude Code: only auto-load for matching file patterns |

OpenCode reads `name`, `description`, and `metadata.opencode/autoinvoke`. Set `opencode/autoinvoke: false`, or `disable-model-invocation: true`, to keep a skill out of the model's available list.

### 3.2 Why is the description the critical field?

The description is the only text the model sees before deciding to load the skill.

- **Too broad:** "Helps with code." The agent loads it for everything and wastes context.
- **Too narrow:** "Formats a changelog for v3.2.1." The agent never finds it.
- **Good:** "Prepare release notes, version bumps, and a GitHub release. Use when the user asks to cut a release."

Include the trigger, the task, and the boundary. Put the key use case first.

### 3.3 How should I name a skill?

Use lowercase kebab-case for portable skills:

```text
^[a-z0-9]+(-[a-z0-9]+)*$
```

The source says "skill name = folder name." In Claude Code, the skill name comes from the directory or the frontmatter `name`. In OpenCode, the ID comes from the path, and the frontmatter `name` is only a display label. Keep them aligned to avoid confusion.

---

## 4. Loading and Context

The source's central claim is correct: skill prompts are not loaded into context unless the skill is needed.

| Stage            | What loads                                |
| ---------------- | ----------------------------------------- |
| Every model step | Skill ID, name, and description           |
| Skill invocation | Full `SKILL.md` body, without frontmatter |
| Supporting files | Only when the agent reads them            |

This is why **references** matter. Put long policies, examples, and data in `references/` and tell the agent which file to read. The agent loads the part it needs instead of the whole skill.

> ⚠️ Verify: The source says "100 skills = 1 MCP tool." Treat that as a rough comparison, not a measured ratio. A skill costs its description; an MCP server costs every tool name, description, and schema it exposes. Both costs depend on how much text each one adds.

> ~~⚠️ Verify: The source says "openclaw magic is SKILLs." "OpenClaw" is not defined in the note and could not be verified. Do not treat this as a technical claim about a specific product.~~

---

## 5. Where to Store Skills

| Host                       | Project                            | Global                                      |
| -------------------------- | ---------------------------------- | ------------------------------------------- |
| **Claude Code**            | `.claude/skills/<name>/SKILL.md`   | `~/.claude/skills/<name>/SKILL.md`          |
| **OpenCode**               | `.opencode/skills/<name>/SKILL.md` | `~/.config/opencode/skills/<name>/SKILL.md` |
| **OpenCode compatibility** | `.claude/skills`, `.agents/skills` | `~/.claude/skills`, `~/.agents/skills`      |

The source says `~/Projects/YourProject/.claude/skills` or `.agents/skills`. Both work in OpenCode as compatibility paths, but `.opencode/skills` is the native location. For Claude Code, `.claude/skills` is native.

Commit project skills so the team gets the same behavior. Keep personal skills in the global folder.

---

## 6. Skills vs MCP vs Subagents

| Mechanism      | What it provides                       | Context cost                              | Best for                                     |
| -------------- | -------------------------------------- | ----------------------------------------- | -------------------------------------------- |
| **Skill**      | Instructions and optional files        | Description always; body on demand        | Repeated procedures, conventions, checklists |
| **MCP server** | External tools and data                | Tool schemas and descriptions             | Reaching systems outside the repo            |
| **Subagent**   | A separate worker with its own context | Own model calls; result returns to parent | Research, review, parallel work              |

A skill does not execute code by itself. It tells the agent how to do something with the tools it already has. An MCP server adds new tools. A subagent gives the work a separate context window.

---

## 7. Recommended Skills

The source lists these as the most important skills to install:

- **agent-browser** — browser automation for the agent.
- **grill-me** — interrogates the plan or implementation.
- **frontend-design** — UI and layout guidance.
- **Vercel React skills** — React and Next.js best practices.
- **Anthropic common skills** — the standard set shipped or published by Anthropic.

> ⚠️ Verify: These are third-party or community recommendations. Check the source, license, and permissions before installing a skill, and read `SKILL.md` before letting it load tools.

---

## 📌 Summary

| Rule                                    | Why                                                               |
| --------------------------------------- | ----------------------------------------------------------------- |
| Put the trigger in the description      | It is the only part the model sees before loading                 |
| Keep `SKILL.md` focused                 | The body stays in context after it loads                          |
| Move long detail into `references/`     | The agent reads only what the task needs                          |
| Use kebab-case and match folder to name | Portable IDs and predictable invocation                           |
| Prefer the native folder for your host  | `.claude/skills` for Claude Code, `.opencode/skills` for OpenCode |
| Do not confuse skills with MCP          | Skills add instructions; MCP adds tools                           |
| Review third-party skills               | A skill can direct the agent to use powerful tools                |
