# OpenCode

Converted from `raw/open code course.md`. Behavior checked against the OpenCode v2 docs on 2026-11-07. Outdated or unverifiable source claims are kept in `> ⚠️ Verify:` callouts.

## 1. What OpenCode Is

OpenCode is an open-source coding agent from Anomaly. It runs in the terminal, as a desktop app, on mobile, and through an SDK. The current major version is v2.

### 1.1 What are the harness, provider, and model layers?

The source asks for the difference between OpenCode/Claude Code, providers, and models. They are three layers.

| Layer        | What it is                                                           | Examples                                             |
| ------------ | -------------------------------------------------------------------- | ---------------------------------------------------- |
| **Harness**  | The agent loop: UI, tools, permissions, context management, sessions | OpenCode, Claude Code, Codex                         |
| **Provider** | The API service that serves a model and bills for tokens             | Anthropic, OpenAI, Azure, Amazon Bedrock, OpenRouter |
| **Model**    | The actual weights that generate output                              | Claude, GPT, Gemini, local models                    |

The harness decides how the agent works. The provider decides where requests go. The model decides what answers. You can run the same model through different providers, and the same harness with different models.

OpenCode addresses a model as `provider/model`, with an optional variant:

```text
anthropic/claude-sonnet-5-5#high
```

### 1.2 How do I install OpenCode?

```bash
# macOS, Linux, WSL (recommended)
curl -fsSL https://opencode.ai/v2/install | bash
```

| Method   | Command                                             |
| -------- | --------------------------------------------------- |
| curl     | `curl -fsSL https://opencode.ai/v2/install \| bash` |
| Homebrew | `brew install anomalyco/tap/opencode-v2`            |
| npm      | `npm install -g @opencode/cli`                      |
| Bun      | `bun install -g --trust @opencode/cli`              |

Open OpenCode inside the project, not in your home directory:

```bash
cd ~/code/my-project
opencode
```

You can also pass the directory directly:

```bash
opencode ~/code/my-project
```

---

## 2. Connect a Provider

### 2.1 How do I connect an account or API key?

Inside the TUI, run `/connect` and follow the provider flow. From a shell:

```bash
opencode auth login anthropic
opencode auth login anthropic --method key
opencode auth list
```

Use a subscription login where the provider supports it; use an API key only when you accept per-token billing. `opencode models` lists the models available to the connected provider.

### 2.2 Does Arabic consume more tokens than English?

Usually, yes, but the ratio depends on the tokenizer. Tokenizers are trained mostly on English text, so Arabic, code-switching, and other non-English text often split into more tokens for the same meaning. There is no fixed multiplier; check the token count for the specific model.

### 2.3 What is "AI thinking"?

Reasoning models can spend extra tokens on an internal reasoning pass before answering. OpenCode exposes model variants such as low, medium, and high where the provider supports them.

- `#variant` in a model string, for example `anthropic/claude-sonnet-5-5#high`.
- `Ctrl+T` cycles variants in the TUI.
- `/variants` lists them.
- Higher variants cost more tokens and time; lower variants fit simple edits.

Variant names and availability are model-specific. The source's "Low/None for ridiculous things" is a heuristic, not a rule.

---

## 3. Build, Plan, and Prompting

### 3.1 Are Build and Plan modes or agents?

The source corrects itself: "Modes aren't modes actually, they're primary agents."

OpenCode has a `mode` field with three values:

| Mode       | Behavior                                               |
| ---------- | ------------------------------------------------------ |
| `primary`  | Runs as the main agent for a session                   |
| `subagent` | Runs only in a child session through the subagent tool |
| `all`      | Can run as either                                      |

Build and Plan are built-in **primary agents**. Switch agents with `Shift+Tab`, `/agents`, or `Ctrl+X` then `A`.

### 3.2 Should I always start in Plan?

Start with Plan for unfamiliar or risky work. Plan explores and proposes changes without editing normal project files. It can still write OpenCode plan files when asked, and shell commands remain permission-controlled, so "read-only" means read-only in practice, not a hard sandbox.

The source says "Plan then Build is ONLY for simple projects/tools." Treat that as a rule of thumb. Complex work benefits from Plan too, but it needs to be split into smaller tasks and delegated.

### 3.3 How do I write a good prompt?

- **State the idea clearly.** Name the feature, the files, and the expected result.
- **Add detail.** Framework, constraints, error text, screenshots, and examples.
- **Do not assume intent.** End with: "Ask me questions to make sure you understand the task before writing code."
- **Attach context.** Type `@` to search files and attach them, for example `@src/auth.ts`.
- **Use shell mode.** Start a line with `!` to run a command, for example `!git status`.
- **Keep one task per prompt.** Small scope gives better error catching.

Permission and question popups appear when the agent needs approval. Navigate them with the arrow keys and confirm with `Enter`; `Esc` cancels or interrupts.

### 3.4 How do I continue after a plan?

Open a new session and attach the plan file:

```text
@plan.md Implement step 1 only. Ask me questions first.
```

Then let the agent run the app and give you the URL, or run it yourself. Paste errors and screenshots instead of describing them from memory.

### 3.5 How do I undo and redo?

`/undo` stages a rollback of the latest user message and the work after it. The prompt returns to the composer. `/redo` cancels the staged rollback.

Shortcuts: `Ctrl+X` then `U` for undo, `Ctrl+X` then `R` for redo.

Snapshots restore files only inside a Git worktree, and only tracked or eligible untracked files in the session's active directory. They are not a backup and do not reverse shell side effects, commits, databases, or files outside the directory.

---

## 4. Sessions

### 4.1 How do I continue an earlier session?

| Action                                         | How                                                          |
| ---------------------------------------------- | ------------------------------------------------------------ |
| Open the session list                          | `/sessions` or `Ctrl+X` then `L`                             |
| Continue the last session from a shell         | `opencode run --continue`                                    |
| Continue a specific session                    | `opencode run --session ses_4f2a1c`                          |
| Start the mini interface on an earlier session | `opencode mini --continue` or `opencode mini --session <id>` |
| New session                                    | `/new` or `Ctrl+X` then `N`                                  |

> ⚠️ Verify: The source says `opencode -c` or `opencode -s ID`. Those flags are not in the current v2 CLI docs. Use `--continue` / `--session` with `opencode run` or `opencode mini`, or `/sessions` in the TUI.

### 4.2 Can I share or fork a session?

- **Share/unshare:** The v2 docs do not document a session share command. The source may refer to a v1 feature or a community plugin.
- **Fork:** OpenCode has a `session_fork` keybind action, but no default key. The source marks it "advanced"; the exact workflow is not documented in the pages reviewed here.

### 4.3 Why does a long session lose quality?

Every request resends the conversation. As the session grows, more tokens compete for attention, and the model gets worse at using the details. This is context rot.

### 4.4 What does compaction do?

Compaction replaces older conversation with a summary and keeps recent messages beside it. OpenCode keeps about 15,000 recent tokens by default.

- **Automatic:** on by default, triggered near the model's context limit with a buffer, default 15% of the window.
- **Manual:** request it at any time through the API.
- **Lossy:** details are summarized. Raise `compaction.keep.tokens` when exact recent detail matters.
- **Model:** uses the session model unless `agents.compaction.model` is set.

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "compaction": {
    "auto": true,
    "keep": { "tokens": 15000 }
  }
}
```

> ⚠️ Verify: The source recommends compacting at "40%–50%." That is a context-rot heuristic, not an OpenCode default. OpenCode's automatic trigger is based on the context window and buffer.

### 4.5 What are the useful session commands?

| Command             | Purpose                                                    |
| ------------------- | ---------------------------------------------------------- |
| `/editor`           | Edit the current prompt in `$VISUAL` / `$EDITOR`           |
| `/models`           | Choose a model                                             |
| `/timeline`         | Open the session timeline (`Ctrl+X` then `G`)              |
| `/variants`         | List model variants                                        |
| `Ctrl+T`            | Cycle variants                                             |
| `Ctrl+P`            | Command palette                                            |
| `/export`-style CLI | `opencode session export <id>`; add `--sanitize` to redact |

The source's "Ctrl+Shift+T for a new terminal tab" is your terminal emulator's shortcut. OpenCode tabs use `Ctrl+Tab` and `Ctrl+Shift+Tab`; `Ctrl+Shift+T` reopens the last closed tab.

---

## 5. Desktop App

The desktop app is available for macOS, Windows, and Linux, and can be installed with:

```bash
brew install --cask opencode-desktop
```

Capabilities:

- **Parallel sessions.** Each session runs in its own tab; jump between them as they finish.
- **Diffs.** Review changes as a diff and comment on specific lines.
- **Built-in browser.** Open the app beside the agent and watch changes land.
- **Cross-device.** Start on desktop, continue in the terminal or on mobile.

The source says to enable "show agents / server status / command palette" in the bottom of Settings. Menu labels change between releases; look for those toggles in the desktop settings if the exact location differs.

---

## 6. Primary and Subagents

### 6.1 What is the difference?

- **Primary agent.** The agent you talk to. It owns the session and can spawn subagents.
- **Subagent.** A child session with fresh context. It runs a delegated task and returns a result. You do not chat with it directly.
- **Hybrid agent.** Set `mode: all` so the same agent can be a primary you talk to and a subagent other primaries can spawn. The source's "Bug Hunter" is this pattern.

Subagents inherit neither your conversation nor your loaded skills. They get a task description, their own system prompt, model, and permissions.

> Source note: the raw note includes a screenshot of the agent hierarchy. It is not reproduced here because the image lives outside the repo.

### 6.2 Which agents are built in?

| Agent       | Mode       | Purpose                                                   |
| ----------- | ---------- | --------------------------------------------------------- |
| **Build**   | `primary`  | Default coding agent; broad tool access                   |
| **Plan**    | `primary`  | Explores and plans without editing normal project files   |
| **General** | `subagent` | Research and multi-step work; cannot spawn more subagents |
| **Explore** | `subagent` | Searches and reads code or web sources without editing    |

> ⚠️ Verify: The source lists a "Scout Agent" for docs and best practices. OpenCode v2 has no built-in `scout` agent. Use Explore, General, or a custom subagent instead.

### 6.3 What is an orchestrator agent?

A primary agent configured mainly to delegate. Give it `subagent` permission but deny `edit`:

```jsonc
{
  "agents": {
    "orchestrator": {
      "mode": "primary",
      "permissions": [
        { "action": "edit", "resource": "*", "effect": "deny" },
        { "action": "subagent", "resource": "*", "effect": "allow" }
      ]
    }
  }
}
```

This keeps the main context clean: subagents do the file work, and the orchestrator only coordinates.

### 6.4 How do I spawn a subagent?

Ask the primary agent to use one:

```text
Use the explore subagent to find every place authentication is handled.
```

> ⚠️ Verify: The source uses `@explore` and `@general`. In OpenCode, `@` in the TUI searches and attaches files; it is not the subagent invocation syntax. Claude Code supports `@agent` mentions, but OpenCode subagents run through the `subagent` tool. Do not copy the `@` habit between harnesses.

Parallel delegation is allowed. It costs more tokens because each subagent runs its own context and model calls.

### 6.5 How do I view subagents while they work?

`Ctrl+X` is the leader key, not a direct subagent view.

| Keys               | Action                       |
| ------------------ | ---------------------------- |
| `Ctrl+X`, then `↓` | First child session          |
| `←` / `→`          | Cycle child sessions         |
| `↑`                | Return to the parent session |
| `Esc`              | Interrupt the running agent  |

### 6.6 How do I create a custom agent?

Create a Markdown file in one of these locations:

```text
~/.config/opencode/agents/<name>.md   # global
.opencode/agents/<name>.md            # project
```

```markdown
---
description: Reviews changes for correctness and regressions
mode: subagent
model: anthropic/claude-sonnet-5-5#high
permissions:
  - action: edit
    resource: "*"
    effect: deny
  - action: shell
    resource: "*"
    effect: deny
---

Review the current changes. List findings in severity order with file and line references.
```

You can also define agents under `agents` in `opencode.json` / `opencode.jsonc`.

### 6.7 What permissions can an agent have?

| Action                  | Covers                       |
| ----------------------- | ---------------------------- |
| `shell`                 | Shell commands               |
| `edit`                  | Edit, write, and patch tools |
| `subagent`              | Child agents                 |
| `read`, `glob`, `grep`  | Local discovery tools        |
| `webfetch`, `websearch` | Web tools                    |
| `skill`                 | Skill loading                |

Effects are `allow`, `ask`, and `deny`. Rules are evaluated in order; the last match wins.

> ⚠️ Verify: The source lists "bash, ListFiles, taskToSpawnAgents, todoTasks, LSP" and similar names. Those are Claude Code-style tool names. OpenCode v2 uses the action names in the table above.

### 6.8 What is SOC2?

SOC 2 is an audit report for service organizations, covering controls around security, availability, processing integrity, confidentiality, and privacy. It describes how a vendor manages data; it is not a technical feature of an agent. The source raises it as a side question.

---

## 7. Skills, MCP, and Plugins

### 7.1 How do skills work in OpenCode?

A skill is reusable instructions in a `SKILL.md` file. OpenCode advertises the skill's name and description, and loads the body only when the skill is used.

- **Project skills:** `.opencode/skills/<skill-id>/SKILL.md`
- **Global skills:** `~/.config/opencode/skills/<skill-id>/SKILL.md`
- **Compatibility paths:** `.claude/skills`, `.agents/skills`, and their global equivalents
- **Flat form:** `skills/<name>.md` at the source root

The skill ID comes from the path, not the frontmatter `name`; use lowercase kebab-case.

**Permissions.** Use the `skill` action with the skill ID as the resource. Rules can allow, ask, or deny loading. Skill permissions are an advanced topic; the default is permissive, so review third-party skills.

### 7.2 How do I add an MCP server?

MCP servers add external tools to the agent. They consume context because tool names, descriptions, and schemas load into the model.

```bash
opencode mcp add context7 --url https://mcp.context7.com/mcp
opencode mcp list
```

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "servers": {
      "my-server": {
        "type": "local",
        "command": ["npx", "-y", "example-mcp-server"]
      }
    }
  }
}
```

Use only the servers you need. More servers mean more tools, more descriptions, and less attention for the actual task.

### 7.3 When should I use plugins?

Plugins extend OpenCode's server, TUI, or GUI. They are an advanced feature; most users do not need them.

```bash
opencode plugin list
opencode plugin add @example/opencode-tui
```

> ⚠️ Verify: The source says "99% of the time you won't use it." That is an opinion, but a reasonable default for beginners.

---

## 📌 Summary

| Area       | Key point                                                                    |
| ---------- | ---------------------------------------------------------------------------- |
| Install    | `curl -fsSL https://opencode.ai/v2/install \| bash`                          |
| Providers  | Connect with `/connect` or `opencode auth login`                             |
| Build/Plan | They are primary agents, not modes; switch with `Shift+Tab` or `/agents`     |
| Prompting  | Attach files with `@`, run shell with `!`, and ask the agent to question you |
| Sessions   | `/sessions`, `Ctrl+X` then `L`, or `opencode run --continue`                 |
| Undo       | `/undo` / `/redo`; snapshots need a Git worktree                             |
| Compaction | Automatic near the limit; manual when you want a clean handoff               |
| Subagents  | Fresh context, parallel work, higher token cost                              |
| Skills     | `SKILL.md`; `.opencode/skills` plus compatibility paths                      |
| MCP        | External tools; load only what you need                                      |
| Plugins    | Advanced; skip unless a specific plugin solves a problem                     |

---
