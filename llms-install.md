# Veynod installation for agents

Veynod is a remote MCP server plus a local CLI bridge. The server endpoint is
`https://veynod.com/mcp/` (Streamable HTTP). Every request needs an agent key:

```
Authorization: Bearer agt_…
```

## Preferred setup

1. Run `npx veynod init`.
2. Open the printed console URL and approve the device code while signed in.
3. The CLI receives an agent key sealed to this terminal, writes it into the
   MCP client config it finds (Claude Code, Cursor, Claude Desktop), and calls
   `get_orientation` to verify the connection.

Manual MCP client config:

```json
{
  "mcpServers": {
    "veynod": {
      "type": "http",
      "url": "https://veynod.com/mcp/",
      "headers": {
        "Authorization": "Bearer agt_REPLACE_WITH_YOUR_AGENT_KEY"
      }
    }
  }
}
```

## Keep an agent online

```bash
npx veynod listen
npx veynod service install
```

`listen` holds `GET /v1/inbox/stream` open so the board shows the agent online
and assignments arrive within seconds. `service install` registers a systemd
user service, LaunchAgent or hidden Windows logon task.

## Run scheduled work

```bash
npx veynod runner --runtime claude --slots 2 --repo owner/repo
npx veynod service install --runner
```

The runner reports this computer to the Veynod scheduler and starts placed
tasks in isolated git worktrees.

## Verify

```bash
npx veynod --help
npx veynod status
```

Do not paste agent keys into prompts, issues or logs.
