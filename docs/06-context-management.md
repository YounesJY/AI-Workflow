# Context Management

## 1. How LLMs Use Context

An LLM takes input tokens and generates output tokens. It repeats that loop until it reaches a stop condition. During a session, each request includes the system prompt, the tool definitions, your prompt, and the conversation so far.

The model itself has no persistent memory. The session feels continuous because the harness resends the transcript on every request, not because the model remembers.

| Term               | Meaning                                                         |
| ------------------ | --------------------------------------------------------------- |
| **Context window** | The maximum tokens a model can process in one request           |
| **Input tokens**   | System prompt, tools, files, conversation history, your message |
| **Output tokens**  | The model's response, including reasoning tokens on some models |
| **Context rot**    | Quality loss as the context fills with more material            |

The source's summary is right: "LLMs have no brain/awareness, no thinking and no memory." More precisely, the model has no memory between calls; the conversation is reconstructed from the transcript each time.

---

## 2. Why Context Is Important

Context is details. The more relevant detail the model has, the more accurate the answer. The less detail, the more the model fills gaps with guesses.

- **Precise prompts beat short prompts.** Name the file, the error, the expected behavior, and the constraint.
- **Attachments beat descriptions.** Paste the stack trace, attach the screenshot, link the reference.
- **Relevant context beats volume.** A focused 2,000 tokens can outperform 50,000 tokens of unrelated history.

The source says ambiguous, detail-less prompts fail because the model does not know your intent. That is the main failure mode in agent work.

---

## 3. Context Rot

The longer a session runs, the more the context fills. Performance degrades: the model misses details, repeats work, contradicts earlier decisions, and follows stale instructions.

The source recommends keeping context around 40%–50%. Treat that as a headroom heuristic, not a hard threshold. The useful version is:

- **Watch the fill level.** Claude Code shows it with `/context`; OpenCode reports usage in its UI and `opencode stats --cost`.
- **Compact or hand off before the window is full.** Waiting until 95% leaves no room for the task.
- **Start fresh for a new feature.** A clean session with a handoff file beats a long session with 50 turns of history.

> ⚠️ Verify: The 40%–50% figure is the source's rule of thumb. It is not a documented limit for any model. The right threshold depends on the task, the model, and how much of the context is still relevant.

---

## 4. Lost in the Middle

Research on long-context attention describes a U-shaped curve: models use the beginning and the end of the context best, and lose details buried in the middle.

- **Strongest:** the system prompt and the most recent message.
- **Weakest:** the middle of a long transcript or a large pasted document.
- **Effect:** the longer the context, the more likely a middle detail is ignored.

Practical consequences:

- Put the task and constraints near the end of the prompt, not only at the top.
- Put the most important reference close to the question.
- Split long documents and retrieve the relevant section instead of pasting everything.
- Restate the goal when a long session drifts.

The source says "the less a context is, the more LLM accurate/precise/smart is." That is directionally correct: a smaller, cleaner context usually produces better focus.

---

## 5. How to Manage Context

### 5.1 How do I avoid unrelated side questions?

Every side question adds turns and tokens to the same conversation. Keep project work in the project session. Use a separate session or the harness's side-question feature for unrelated questions.

- **OpenCode:** `/btw <question>` answers without adding to the conversation.
- **Claude Code:** start a new session, or use a side question feature if your version provides one.

> ⚠️ Verify: The source says to "revert" side questions. The right action depends on the harness: some support non-persistent side questions, some require a new session, and some require rewinding.

### 5.2 What is a handoff?

A handoff is a written summary that lets a fresh session continue the work without the old transcript.

```markdown
# Handoff

## Goal
Add rate limiting to the public API.

## Done
- Added the limiter middleware in `src/middleware/rate-limit.ts`.
- Wired it into `src/app.ts`.
- Tests pass for the auth routes.

## Next
- Add tests for the upload endpoint.
- Decide whether the limit is per IP or per API key.

## Decisions
- Using a token bucket, not a fixed window.
- Redis is already available; do not add a new dependency.

## Files
- `src/middleware/rate-limit.ts`
- `src/app.ts`
- `tests/auth.test.ts`
```

Then:

```text
Read @handoff.md and continue from "Next". Ask me questions before editing.
```

### 5.3 What does compaction do?

Compaction replaces older conversation with a summary and keeps recent messages. It is automated in both Claude Code and OpenCode, and can be requested manually.

- **Claude Code:** `/compact`, optionally with instructions about what to preserve.
- **OpenCode:** automatic by default near the context limit; manual compaction through the API; `compaction.keep.tokens` controls how much recent detail stays.

Compaction is lossy. It preserves the shape of the work, not every detail. For a clean break, a handoff file is usually better.

### 5.4 When should I use subagents?

Subagents run in a separate context and return a summary. They keep research and failed attempts out of the main conversation. They cost extra tokens because each one makes its own model calls.

Use a subagent for exploration, research, and independent review. Do not use one for a task that needs the full conversation.

### 5.5 How should I use AGENTS.md or CLAUDE.md?

The source calls this a "very important guidance file" and compares it to an FAQ. That is accurate: it tells the agent what it needs to know and where to search, without spending context on trial and error.

Keep it short. Include build commands, conventions, architecture pointers, and hard rules. Remove stale or contradictory instructions. A bloated file loads on every request and competes with the task.

---

## 6. Common Context Mistakes

- **Vague prompts.** "Fix the bug" gives the agent nothing to target.
- **Missing artifacts.** Describe the error instead of pasting it, or describe the UI instead of attaching a screenshot.
- **One giant session.** Ten features in one conversation guarantees context rot.
- **Pasting everything.** A 100-page document in the middle of the context is likely to be ignored.
- **Stale instructions.** An outdated `AGENTS.md` or `CLAUDE.md` is worse than no file.
- **No handoff.** Starting fresh without a summary repeats work and loses decisions.

The source recommends a context visualizer to see where tokens go. Claude Code has `/context`; OpenCode has usage and cost reporting. A community "claude-context-visualizer" tool is mentioned in the source but was not verified here.

> ⚠️ Verify: "claude-context-visualizer" is a third-party tool name from the source. Confirm the repository and what data it reads before installing it.

---

## 📌 Summary

| Principle                       | Action                                                           |
| ------------------------------- | ---------------------------------------------------------------- |
| Context is detail               | Give the model the files, errors, and constraints it needs       |
| Context has a limit             | Watch the fill level; do not wait for 100%                       |
| Context rot is real             | Keep sessions short; one feature per session                     |
| Attention is U-shaped           | Put key instructions at the start and repeat the goal at the end |
| Handoff beats a long transcript | Write `handoff.md`, start fresh, read it                         |
| Compaction is lossy             | Use it to continue, not to preserve every detail                 |
| Subagents isolate work          | Delegate research and review; accept the token cost              |
| Keep the guidance file short    | `AGENTS.md` / `CLAUDE.md` should be current and specific         |
