# MCP Servers

## 1. What Is MCP?

MCP (Model Context Protocol) is an open standard for connecting an AI application to external tools and data. It was created by Anthropic and is now an open protocol with multiple clients and servers.

The source describes it as "a unified form that lets systems and tools make your agent more capable." That is the right idea: instead of every harness building a custom integration for every service, the service exposes an MCP server and any MCP client can use it.

| Piece      | Role                                                             |
| ---------- | ---------------------------------------------------------------- |
| **Host**   | The agent application, such as Claude Code or OpenCode           |
| **Client** | The connection inside the host that talks to one server          |
| **Server** | The process or service that exposes tools, resources, or prompts |

An MCP server can provide:

- **Tools** — actions the model can call.
- **Resources** — data the model can read.
- **Prompts** — reusable prompt templates.

### 1.1 Why not just use built-in tools?

Built-in tools cover files, shell commands, and web access. MCP covers everything else: issue trackers, databases, design tools, browser automation, internal APIs, and third-party services.

> ⚠️ Verify: The source says MCP is "the protocol set by Anthropic." Anthropic created it, but it is an open standard, not a proprietary Anthropic-only interface.

---

## 2. Why MCP Matters

- **Reach.** The agent can act on systems that are not part of the local repo.
- **Reuse.** One server works across MCP-compatible hosts.
- **Boundaries.** The server defines which operations are exposed, so you can give the agent a narrow API instead of broad shell access.
- **Composability.** A host can connect several servers and combine their tools.

The cost is context. Every connected server contributes tool names, descriptions, and schemas to the model's prompt. A large MCP setup can crowd out the actual task.

---

## 3. The Context Cost

The source states the tradeoff directly: the more MCP servers you add, the more tool descriptions load into context, and the less accurate the agent becomes. Use only the servers you need.

| Symptom                             | Cause                                         |
| ----------------------------------- | --------------------------------------------- |
| The agent picks the wrong tool      | Too many similar tool descriptions            |
| Answers get vaguer in long sessions | Tool schemas and results consume context      |
| The agent forgets project details   | Context is spent on tools instead of the task |
| Higher token bills                  | Every request carries the tool definitions    |

Practical rules:

- **Start with zero.** Add a server when a real task needs it.
- **Prefer narrow servers.** A server with five relevant tools beats one with fifty.
- **Disable unused servers.** Keep the config but set `disabled` where the host supports it.
- **Re-check after a month.** Remove servers you stopped using.

> ⚠️ Verify: The source says more servers make the agent "less intelligent/accurate." That is accurate as a tendency, not a measurable law. The effect depends on how many tools each server exposes and how large the descriptions are.

---

## 4. How Do I Add a Server?

### 4.1 How do I add a server in Claude Code?

```bash
# Remote HTTP server
claude mcp add --transport http notion https://mcp.notion.com/mcp

# Local stdio server
claude mcp add --transport stdio airtable -- npx -y airtable-mcp-server

# List configured servers
claude mcp list
```

Claude Code supports local, project, and user scopes. A project-scoped `.mcp.json` can be committed so the team shares the same servers. The `type` field accepts `http` (or `streamable-http`), `sse`, `stdio`, and `ws`.

### 4.2 How do I add a server in OpenCode?

```bash
opencode mcp add context7 --url https://mcp.context7.com/mcp
opencode mcp add everything --global -- npx -y @modelcontextprotocol/server-everything
opencode mcp list
```

Or configure it by hand:

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

Remote servers use OAuth by default. If the list shows `needs authentication`, run `/mcps` in the TUI, select the server, and sign in.

### 4.3 Which transport should I use?

| Transport                  | Use for                                                  |
| -------------------------- | -------------------------------------------------------- |
| `stdio`                    | A local process launched by the host                     |
| `http` / `streamable-http` | A remote server over HTTP                                |
| `sse`                      | Older remote servers that only expose Server-Sent Events |
| `ws`                       | A persistent bidirectional connection                    |

Prefer HTTP for remote servers unless the server only supports SSE or WebSocket.

---

## 5. Security and Maintenance

MCP servers run with the access you give them. Treat each one as a third-party dependency.

- **Review before connecting.** Read the server's permissions and source.
- **Use least privilege.** Pass only the API scopes the task needs.
- **Protect secrets.** Prefer environment variables or OAuth over hard-coded keys in committed config.
- **Pin versions.** `npx -y package` can pull a new release; pin a version for repeatable behavior.
- **Audit the tool list.** Remove servers that expose destructive or broad tools you do not use.
- **Check logs when a call fails.** Authentication and transport errors are the common failures.

> ⚠️ Verify: The source does not cover security. This section is added from general MCP practice; verify the specifics for each server you connect.

---

## 📌 Summary

| Question            | Answer                                                               |
| ------------------- | -------------------------------------------------------------------- |
| What is MCP?        | An open standard for connecting agents to external tools and data    |
| Why use it?         | To reach systems that built-in tools do not cover                    |
| What does it cost?  | Context: tool names, descriptions, and schemas load on every request |
| How many servers?   | Only the ones a current task needs                                   |
| Claude Code command | `claude mcp add --transport http <name> <url>`                       |
| OpenCode command    | `opencode mcp add <name> --url <url>`                                |
| Main risk           | Over-broad tools, secrets in config, and context bloat               |
