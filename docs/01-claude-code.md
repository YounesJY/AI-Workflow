# Claude Code

## 1. Installation & Accounts

### 1.1 Do I need a paid plan?

Yes. There is no free tier for Claude Code.

| Access path                       | Who it fits                                                      |
| --------------------------------- | ---------------------------------------------------------------- |
| **Claude Pro / Max / Ultra**      | Individual use with a claude.ai subscription                     |
| **Claude for Teams / Enterprise** | Teams that need central billing and admin controls               |
| **Claude Console**                | Pay-as-you-go API billing                                        |
| **Cloud providers**               | Amazon Bedrock, Google Cloud's Agent Platform, Microsoft Foundry |

The source only mentions a subscription or a Console account. Cloud-provider access also exists.

### 1.2 How do I install Claude Code?

Use the native installer for your platform:

```bash
# macOS, Linux, WSL
curl -fsSL https://claude.ai/install.sh | bash
```

```powershell
# Windows PowerShell
irm https://claude.ai/install.ps1 | iex
```

| Method               | Command                                           | Auto-updates             |
| -------------------- | ------------------------------------------------- | ------------------------ |
| Native (recommended) | `curl -fsSL https://claude.ai/install.sh \| bash` | Yes                      |
| Homebrew             | `brew install --cask claude-code`                 | No; run `brew upgrade`   |
| WinGet               | `winget install Anthropic.ClaudeCode`             | No; run `winget upgrade` |

Verify the install, then start it inside a project:

```bash
claude --version
cd ~/code/my-project
claude
```

On first launch, Claude Code opens a browser login. If the browser does not open, press `c` to copy the login URL.

### 1.3 Why should I avoid pay-as-you-go API billing?

API billing charges per token, so a long or runaway session can get expensive. A subscription moves that cost into a fixed plan.

- **Check the active credential.** `/status` shows whether you are using a subscription login or an API key.
- **Unset the key if needed.** `unset ANTHROPIC_API_KEY` falls back to your subscription.
- **Watch long autonomous runs.** `auto` and `bypassPermissions` can spend a lot without asking.

### 1.4 Is Next.js a good default stack?

The source recommends Next.js because it is "mostly preferred by AI" and easy for beginners. Claude Code does not require any stack. Pick a framework you can run, debug, and review yourself; a familiar stack produces better prompts and fewer blind fixes.

> ⚠️ Verify: "Preferred by AI" is a subjective claim, not a benchmark. Treat it as the author's opinion.

---

## 2. Where to Run the Agent

### 2.1 Why not run an agent in my home folder?

Because the working directory defines what the agent can see and change. Launching in `~` turns your entire home tree into the project: personal files, credentials, downloads, and every unrelated repo.

- **Context pollution.** The agent searches a huge tree to find the actual project, wasting tokens and attention.
- **Accidental writes.** A wrong path or a broad command can touch files outside the codebase.
- **Weak blast-radius control.** Permission rules and sandboxing are scoped around the working directory.

> ⚠️ Verify: The source cites an "antigravity accident with 2TB lost." This is unverified. Keep it as a cautionary anecdote, not a documented incident.

### 2.2 Where should I launch it?

Launch from the project root, not from a parent folder or your home directory.

```bash
cd ~/code/my-project
claude
```

- **One project per session.** Keeps context and permissions scoped.
- **Use Git.** Commit before risky work; checkpoints are not a replacement for version control.
- **Add extra directories explicitly.** Use `/add-dir` or `claude --add-dir` instead of starting higher in the tree.

---

## 3. Session Management

### 3.1 How do I exit and resume a session?

Exit with `/exit`, or press `Ctrl+C` twice. The first press clears the prompt; the second exits.

```bash
claude --resume      # or -r: picker, session ID, or session name
claude --continue    # or -c: most recent conversation in this directory
claude --resume auth-refactor
claude --resume <session-id> --fork-session
```

| Command                   | Behavior                                                     |
| ------------------------- | ------------------------------------------------------------ |
| `claude --resume`, `-r`   | Resume a specific session by ID or name, or open a picker    |
| `claude --continue`, `-c` | Resume the most recent conversation in the current directory |
| `--fork-session`          | Create a new session ID instead of reusing the old one       |

### 3.2 How do I rewind a conversation or code change?

Run `/rewind`, or press `Esc` twice when the prompt is empty. The menu lists your prompts and offers:

- **Restore code and conversation** — revert both to that point.
- **Restore conversation** — keep current code, rewind the chat.
- **Restore code** — revert file changes, keep the chat.
- **Summarize from here / up to here** — compress context instead of reverting.
- **Never mind** — close the menu.

Claude Code keeps snapshots for the 100 most recent checkpoints in a session. Snapshots are saved with the conversation, so `/rewind` still works after `--resume`. They are deleted in the retention sweep, by default about 30 days after the session last saved one.

**Limits.**

- Bash-only changes are not tracked unless Claude already edited that file with its editing tools.
- Subagent edits are usually not captured.
- Symlinked and hard-linked files are skipped on restore.
- Checkpoints are session-level recovery, not Git history.

---

## 4. The CLAUDE.md File

### 4.1 What is CLAUDE.md?

A Markdown file of persistent instructions for the project, your user account, or an organization. Claude reads it at the start of every session and treats it as context, not as enforced configuration.

The source calls it "the most important file in your project" and "the portal to your project." That framing holds: it is where you put build commands, conventions, architecture notes, and rules you would otherwise repeat.

### 4.2 When does it load?

| Scope              | Location                               | Applies to                    |
| ------------------ | -------------------------------------- | ----------------------------- |
| **Managed policy** | Platform-specific managed path         | All users on the machine      |
| **User**           | `~/.claude/CLAUDE.md`                  | All your projects             |
| **Project**        | `./CLAUDE.md` or `./.claude/CLAUDE.md` | Everyone in the repo          |
| **Local**          | `./CLAUDE.local.md`                    | You only; add to `.gitignore` |

Files in the working directory and every directory above it load at launch. Files in subdirectories load on demand when Claude reads or edits a file there. All discovered files are concatenated; they do not override each other.

Check what loaded with `/context all` (look under **Memory files**), and edit with `/memory`.

> ⚠️ Verify: The source calls CLAUDE.md "the very 1st file being loaded." It loads at session start, but it is delivered as a user message after the system prompt, and multiple memory files can load together. "First" is a simplification.

### 4.3 Can I use AGENTS.md instead?

Yes, on Claude Code v2.1.277 or later.

| Repository has                                     | Claude reads by default                          |
| -------------------------------------------------- | ------------------------------------------------ |
| `AGENTS.md`, no `CLAUDE.md` or `CLAUDE.local.md`   | `AGENTS.md`                                      |
| `AGENTS.md` and a `CLAUDE.md` or `CLAUDE.local.md` | `CLAUDE.md` only                                 |
| `CLAUDE.md` that imports `AGENTS.md`               | `CLAUDE.md`, with `AGENTS.md` through the import |

Change the behavior with `/config` → **Project instructions**:

- `claude-md-or-agents-md` — default.
- `claude-md-and-agents-md` — read both.
- `claude-md` — read only `CLAUDE.md`.
- `managed-only` — read only the organization's managed file.

To share one file across tools, put this at the top of `CLAUDE.md`:

```markdown
@AGENTS.md
```

> ⚠️ Verify: The source says "You can just call it AGENTS.md" for other agents. That is now accurate for Claude Code, but only when no `CLAUDE.md` or `CLAUDE.local.md` exists in the working directory or above it, unless you change **Project instructions**.

### 4.4 How do I create it?

Create it manually, or run `/init`. `/init` analyzes the repo and generates a starting file with build commands, test instructions, and conventions. If a `CLAUDE.md` already exists, it suggests improvements instead of overwriting it.

For the interactive setup flow, set `CLAUDE_CODE_NEW_INIT=1` before running `/init`.

### 4.5 What goes in `## IMPORTANT NOTES`?

The source's convention: after the file is reviewed and verified, add a `## IMPORTANT NOTES` section and keep it current with major project changes and status.

Keep the whole file short. The official guidance is under 200 lines per file; the source recommends roughly 100. Write concrete instructions, group them under headers, and remove contradictions.

---

## 5. Permission Modes

### 5.1 Which modes exist?

The source's "3 modes" is outdated. Current Claude Code has six.

| Mode                | Runs without asking                               | Use for                             |
| ------------------- | ------------------------------------------------- | ----------------------------------- |
| `default` / Manual  | Reads only                                        | Sensitive work; review every action |
| `acceptEdits`       | Reads, file edits, and common filesystem commands | Iterating on code you will review   |
| `plan`              | Reads; edits blocked until you approve a plan     | Exploring before changing           |
| `auto`              | Everything, with a background classifier          | Long tasks; fewer prompts           |
| `dontAsk`           | Pre-approved actions only; everything else denied | CI and scripts                      |
| `bypassPermissions` | Everything                                        | Isolated containers and VMs only    |

On Claude Code v2.1.283 and later, interactive terminal and VS Code sessions start in `auto` by default. Manual is still the safest option.

### 5.2 What is Manual (`default`) mode?

Claude asks before the first use of each tool that needs approval. Reads inside the working directory run without prompting. This is the safest mode for unfamiliar code and sensitive repositories.

### 5.3 What does `acceptEdits` do?

It auto-approves file edits and common filesystem Bash commands inside the working directory or `additionalDirectories`: `mkdir`, `touch`, `rm`, `rmdir`, `mv`, `cp`, and `sed`. Everything else still prompts, including paths outside scope, protected paths, compound commands, and most other Bash commands.

> 

### 5.4 What is `plan` mode?

Claude researches and proposes changes without editing source files. It can read files, run permission-controlled shell commands, and write a plan. Edits stay blocked until you approve the plan.

### 5.5 What is `auto` mode?

Claude runs without routine prompts while a background classifier checks actions such as shell commands and network requests against your request. It is the current default starting mode on recent versions. Auto is not the same as bypass: the classifier can still block actions.

### 5.6 What is `dontAsk` mode?

Claude auto-denies every call that would otherwise prompt. Pre-approved tools and actions that need no approval still run. Use it for CI pipelines or locked-down scripts where the session must never wait for input.

### 5.7 What is `bypassPermissions` mode?

It skips permission prompts and safety checks, including writes to protected paths such as `.git` and `.claude`. Use it only inside a container, VM, or dev container without access to your host.

- **Enable at launch.** You cannot enter this mode from a session started without it.
- **First-run warning.** Interactive sessions show a responsibility warning and record acceptance in `~/.claude/settings.json`.
- **Root is refused.** On Linux and macOS, Claude Code refuses to start in this mode as root or under `sudo`.
- **Some actions still prompt.** Explicit `ask` rules, critical-path `rm` removals, and cross-session messaging safeguards still apply.

### 5.8 How do I switch or pre-select a mode?

- **During a session:** press `Shift+Tab` to cycle. The default cycle is `default` → `acceptEdits` → `plan`, then back. `auto` appears when available; `bypassPermissions` appears only if it was enabled at launch or in settings.
- **At launch:** `claude --permission-mode plan`.
- **Bypass at launch:** `claude --dangerously-skip-permissions`.
- **Add bypass to the cycle without activating it:** `claude --allow-dangerously-skip-permissions`.
- **As a default:** set `permissions.defaultMode` in a settings file.
- **`dontAsk`:** set it with `--permission-mode dontAsk`; it never appears in the cycle.

> ⚠️ Verify: The source says bypass is "ONLY available on shift+tab whenever you launch it with that command." The meaning is roughly right: bypass must be enabled at launch, then it becomes selectable with `Shift+Tab`.

---

## 6. Custom Permissions & Sandboxing

### 6.1 How do allow, ask, and deny rules work?

Rules use the format `Tool` or `Tool(specifier)`. Evaluation order is **deny → ask → allow**; the first match wins.

| Rule                           | Effect                                                                |
| ------------------------------ | --------------------------------------------------------------------- |
| `Bash`                         | Matches every Bash command; a deny rule removes the tool from context |
| `Bash(npm run *)`              | Matches commands starting with `npm run`                              |
| `Read(./.env)`                 | Matches reading that file                                             |
| `WebFetch(domain:example.com)` | Matches fetches to that domain                                        |

Example `settings.json`:

```json
{
  "permissions": {
    "allow": [
      "Bash(npm run *)",
      "Bash(git status)"
    ],
    "ask": [
      "Bash(git push *)"
    ],
    "deny": [
      "Read(./.env)",
      "Bash(rm -rf *)"
    ]
  }
}
```

Manage rules interactively with `/permissions`. Rules are enforced by Claude Code, not by the model.

### 6.2 Where do the rules live?

| Scope                 | File                               |
| --------------------- | ---------------------------------- |
| User                  | `~/.claude/settings.json`          |
| Project               | `.claude/settings.json`            |
| Local (not committed) | `.claude/settings.local.json`      |
| Managed policy        | Platform-specific managed settings |

Precedence runs from managed settings down to user settings; more specific local files override project files. Deny rules apply in every mode, including `bypassPermissions`. Allow rules have no effect in `bypassPermissions`.

### 6.3 What does `/sandbox` do?

`/sandbox` toggles Claude Code's Bash sandbox. The sandbox restricts filesystem and network access for shell commands and is available on macOS, Linux, and WSL2. It reduces the damage a command can do; it does not replace a container or VM for fully untrusted work.

> ⚠️ Verify: The source says `/sandbox` is "for more advanced users." That is an opinion. It is a supported feature, not an expert-only setting.

---

## 7. Built-in & Custom Commands

### 7.1 What does `/add-dir` do?

It adds a working directory for the current session, for files outside the project root.

```text
/add-dir ../shared-lib
```

The CLI equivalent is `claude --add-dir ../shared-lib`. The addition lasts for that session. To persist it, set `permissions.additionalDirectories` in a settings file.

### 7.2 What does `/chrome` do?

Claude Code has a Chrome integration for browser work: testing a local web app, reading console logs, filling forms, and checking rendered output.

- `/chrome` opens the Claude in Chrome settings.
- `/claude-in-chrome [task]` runs a browser task.
- `claude --chrome` starts with Chrome integration enabled.

The source says there is "a better way and more professional and minimal," but the note is cut off before naming it. That alternative is unknown, so it is not reconstructed here.

### 7.3 What about custom commands?

The source heading mentions custom commands but never covers them. Current Claude Code treats custom commands as skills: a file at `.claude/commands/deploy.md` and a skill at `.claude/skills/deploy/SKILL.md` both create `/deploy`. Existing command files still work; skills add supporting files, frontmatter, and automatic invocation.

---

## 📌 Summary

| Rule                                                                 | Why                                                           |
| -------------------------------------------------------------------- | ------------------------------------------------------------- |
| Use a subscription, not pay-as-you-go, unless you are watching spend | API billing can run up on long sessions                       |
| Launch inside the project, never in `~`                              | Scopes context, permissions, and blast radius                 |
| Start in Plan or Manual for unfamiliar work                          | Read-only first, edit after approval                          |
| Keep `CLAUDE.md` short and specific                                  | It loads every session; vague or bloated files hurt adherence |
| Use `AGENTS.md` only when there is no `CLAUDE.md`                    | That is the current default resolution order                  |
| Treat `bypassPermissions` as container-only                          | It skips prompts and protected-path checks                    |
| Use deny rules for secrets and destructive commands                  | Deny wins over allow in every mode                            |
| Use `/rewind` and Git for different jobs                             | Checkpoints are session recovery, not history                 |

---
