# Subagents



## 1. What Is a Subagent?

A subagent is a child agent spawned by a primary agent. It runs in its own context window, works on a delegated task, and returns a result to the caller.

The source calls it "an exact copy of your primary agent." That is only true for one kind. A **non-fork subagent** starts fresh: it does not see the parent conversation and can have a different system prompt, model, and narrower tools. A **fork** inherits the parent conversation, then returns only its final result. Either way it is a separate worker, not a clone that shares the main context.

|                     | Non-fork subagent                   | Fork                           |
| ------------------- | ----------------------------------- | ------------------------------ |
| Starts with         | A task brief and its own definition | The entire conversation so far |
| Sees parent history | No                                  | Yes                            |
| Model and tools     | From its definition file            | Same as the main session       |

---

## 2. Why Subagents Matter

The source names context saving as the most important feature, and that is correct.

- **Context isolation.** Research, file searches, and failed attempts stay in the child's context. The parent gets a summary, not the whole transcript.
- **Parallelism.** Several subagents can work at once.
- **Specialization.** A reviewer, explorer, or test runner can have its own prompt and permissions.
- **Quality.** Independent reviewers catch issues the primary agent misses.

The cost is tokens. Each subagent makes its own model calls, so parallel work is not free.

---

## 3. When Should I Use a Subagent?

Use one when the task is large enough that the intermediate work would pollute the main context:

- Exploring an unfamiliar codebase.
- Researching docs, APIs, or best practices.
- Reviewing a plan or diff from a different perspective.
- Running independent slices of work in parallel.
- Verifying a change with a separate set of eyes.

Do not use one for a one-line edit or a task that needs the full conversation. The delegation overhead and the fresh-context cost outweigh the benefit.

The source says subagents are "very important for large/complex codebases." That is the right default.

---

## 4. Parallelism

Multiple subagents can run in parallel. This is useful when the slices are independent: frontend, backend, tests, docs.

- **Token cost rises.** Each child has its own context and model calls.
- **Coordination matters.** Give each subagent a clear scope and tell it what not to touch.
- **Review the merge.** Parallel edits can conflict; inspect the combined diff.

The source warns that parallel workflows consume a lot of tokens. Keep the warning.

---

## 5. Claude Code

### 5.1 Which subagents are built in?

| Subagent            | Purpose                                                                                 |
| ------------------- | --------------------------------------------------------------------------------------- |
| **Explore**         | Search and read the codebase without editing; keeps exploration out of the main context |
| **Plan**            | Research for plan mode; gathers context before a plan is presented                      |
| **General-purpose** | Multi-step work that needs exploration and modification                                 |

Claude delegates to them automatically when a task matches. You can also ask for one by name, or use the `@` mention typeahead for custom and plugin subagents.

### 5.2 How do I create a custom subagent?

Custom subagents live in `.claude/agents/` (project) or `~/.claude/agents/` (user). A Markdown file defines the name, description, tools, model, and system prompt. Claude Code also supports JSON configuration and plugin-provided subagents.

### 5.3 What does fork mode do?

`CLAUDE_CODE_FORK_SUBAGENT` controls **fork mode**, not whether subagents exist. With fork mode on, Claude can start a fork on its own: a subagent that inherits the conversation so far, including the system prompt, tools, model, and message history. The fork's tool calls stay out of the main conversation; only the final result comes back.

Fork mode is on by default from Claude Code v2.1.302, and in interactive terminal sessions from v2.1.232. Set `CLAUDE_CODE_FORK_SUBAGENT=0` to turn it off; subagents then start fresh from their own definitions.

|                             | Fork                                       | Non-fork subagent                          |
| --------------------------- | ------------------------------------------ | ------------------------------------------ |
| Context                     | Full conversation history                  | Fresh context with the prompt you pass     |
| System prompt, tools, model | Same as the main session                   | From the subagent definition               |
| Prompt cache                | Shared with the main session               | Separate cache                             |
| Best for                    | A side task that needs the full background | A focused task with a self-contained brief |

A fork cannot spawn further forks. You can start one yourself with `/subtask <task>`, even when fork mode is off. To block forks without changing the environment variable, add `Agent(fork)` to `permissions.deny`.

> ⚠️ Verify: The source says `CLAUDE_CODE_FORK_SUBAGENT=1` is "a very useful flag." `1` is already the default; the meaningful change is setting it to `0`. Fork mode also does not turn every subagent into a fork. It lets Claude choose a fork when the task needs the conversation; a subagent you invoke for a focused job still runs from its own definition.

### 5.4 How do I disable built-in subagents?

Set `CLAUDE_CODE_DISABLE_EXPLORE_PLAN_AGENTS=1` to remove the built-in Explore and Plan subagents. Claude then reads and explores files directly.

### 5.5 What are agent teams?

Agent teams are a separate, experimental feature. They coordinate multiple Claude Code instances with a shared task list and direct messaging. Enable them with:

```bash
CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1
```

|                   | Subagents                                   | Agent teams                                              |
| ----------------- | ------------------------------------------- | -------------------------------------------------------- |
| **Context**       | Own window; result returns to caller        | Own window; fully independent                            |
| **Communication** | Return a result to the caller               | Teammates message each other directly                    |
| **Coordination**  | Main agent manages the work                 | Self-coordinated through messages and a shared task list |
| **Best for**      | Focused tasks where only the result matters | Complex work that needs discussion                       |
| **Token cost**    | Lower                                       | Higher; each teammate is a separate instance             |

> ⚠️ Verify: The source says "same for TEAMS" and that a subagent "won't die and you can still talk and consult." A normal subagent finishes and returns a result; you do not keep chatting with it. Agent teams are the feature that supports direct conversation with teammates, and they require the experimental flag.

---

## 6. OpenCode

### 6.1 What do the agent modes mean?

OpenCode agents have a `mode` field:

| Mode       | Behavior                   |
| ---------- | -------------------------- |
| `primary`  | Main agent for a session   |
| `subagent` | Child session only         |
| `all`      | Either primary or subagent |

Built-ins: **Build** and **Plan** are primary; **General** and **Explore** are subagents. There is no built-in Scout agent in v2.

### 6.2 How do I spawn a subagent?

Ask the primary agent to use a subagent:

```text
Use the explore subagent to map the authentication flow. Do not edit files.
```

In OpenCode, `@` attaches files in the TUI; it is not the subagent syntax. Subagents run through the `subagent` tool.

### 6.3 How do I view and configure subagents?

| Action              | Keys / command                                                  |
| ------------------- | --------------------------------------------------------------- |
| View child sessions | `Ctrl+X`, then `↓`                                              |
| Cycle children      | `←` / `→`                                                       |
| Return to parent    | `↑`                                                             |
| List agents         | `/agents` or `Ctrl+X` then `A`                                  |
| Create an agent     | Markdown in `~/.config/opencode/agents/` or `.opencode/agents/` |

A custom subagent can deny edits and shell access:

```markdown
---
description: Reviews changes for correctness and regressions
mode: subagent
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

---

## 7. The Review Pattern

The source recommends using subagents to review a plan from different perspectives, the same idea used by BugBot and CodeRabbit.

A practical loop:

1. **Plan** with the primary agent.
2. **Spawn reviewers** with different mandates:
   - correctness and edge cases,
   - security and permissions,
   - tests and failure modes,
   - simplicity and scope.
3. **Merge the findings** and decide what to change.
4. **Implement** in the primary agent or delegate independent slices.

A non-fork reviewer starts fresh, so give it the plan and the relevant files explicitly. A fork already has the conversation, so brief it on the review mandate instead. Do not assume a fresh subagent knows anything you did not pass it.

---

## 📌 Summary

| Point            | Takeaway                                                                                             |
| ---------------- | ---------------------------------------------------------------------------------------------------- |
| What it is       | A child agent with its own context window; a non-fork starts fresh, a fork inherits the conversation |
| Main benefit     | Keeps research and failed attempts out of the main context                                           |
| Main cost        | Extra tokens; parallel work multiplies them                                                          |
| Best use         | Large codebases, research, independent review, parallel slices                                       |
| Claude Code      | Built-ins Explore, Plan, General-purpose; `CLAUDE_CODE_FORK_SUBAGENT` is on by default               |
| OpenCode         | `primary` / `subagent` / `all`; built-ins Build, Plan, General, Explore                              |
| Review           | Spawn reviewers with different mandates, then merge findings                                         |
| Not a chat buddy | A normal subagent returns a result; use agent teams for direct collaboration                         |
