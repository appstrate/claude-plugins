# Appstrate plugins for Claude Code

Official [Claude Code](https://code.claude.com) plugin marketplace for [Appstrate](https://appstrate.dev).

## `appstrate` — your organization's skills and MCP connection

Installs the skills of your Appstrate organization (the org and space pinned on your `appstrate` CLI profile) as `/appstrate:<skill>` commands, and keeps them in sync: Claude Code re-runs the sync in the background once per session and reloads the plugin when a skill was published, updated or removed. The same run also refreshes `~/.agents/skills/` for OpenAI Codex.

The connected plugin also configures the organization's Appstrate MCP endpoint.
**Release dependency:** this MCP feature requires the CLI release containing
[appstrate/appstrate#1261](https://github.com/appstrate/appstrate/issues/1261).
An older installed CLI continues to produce a skills-only plugin; update it once
that release is available. The marketplace command itself stays unchanged.

### Requirements

- Claude Code **2.1.229** or later (command-sourced plugins).
- The `appstrate` CLI on your `PATH`, logged in with a pinned organization and space:

  ```sh
  npm install -g appstrate        # or: curl -fsSL https://get.appstrate.dev | bash
  appstrate login
  appstrate space current         # must print a space id
  ```

### Install

```sh
claude plugin marketplace add appstrate/claude-plugins
claude plugin install appstrate@appstrate
```

Claude Code shows the exact command it will run and asks you to accept it. The command uses the installed CLI when available and otherwise falls back to `npx -y appstrate@latest`, then runs `skills sync --target claude-plugin --target codex --print-path`. Only published skill versions are synced; publish a version from the Appstrate dashboard to ship a change.

### MCP authentication and scope

After a connected sync, open `/mcp`, select `plugin:appstrate:appstrate`, and
complete browser OAuth if requested. This is separate from `appstrate login`.
The plugin contains only the HTTP endpoint `<instance>/api/mcp/o/<orgId>`;
no CLI token or headers are copied. An unconfigured machine receives a setup
plugin without MCP until login and a connected sync succeed.

**Skills use the CLI's pinned space; MCP uses the organization's default space.**
For example, skills from pinned space B can execute MCP operations in default
space A. Check `appstrate space list` and `appstrate space current`; use
`appstrate api` for operations in a different pinned space. A space switch does
not change the MCP endpoint.

Plugin tools have names such as
`mcp__plugin_appstrate_appstrate__search_operations`. A manually configured
`appstrate` MCP server can coexist with the plugin server; inspect the
endpoints in `/mcp` and disable the unwanted connection to avoid using the wrong
one. Sync never removes your manual configuration.

To keep skills without the plugin MCP connection, toggle
`plugin:appstrate:appstrate` off in `/mcp` for the current project. Enterprise
`allowedMcpServers` / `deniedMcpServers` rules can match the endpoint URL or the
scoped server name `plugin:appstrate:appstrate`.
See [Claude Code MCP](https://code.claude.com/docs/en/mcp#plugin-provided-mcp-servers)
and [managed MCP configuration](https://code.claude.com/docs/en/managed-mcp).

Codex receives skills only. Configure its MCP connection separately with
`codex mcp add appstrate --url <instance>/api/mcp/o/<orgId>` and
`codex mcp login appstrate`, inspecting any existing entry first. The
[CLI reference](https://github.com/appstrate/appstrate/tree/main/apps/cli#codex-and-running-without-a-claude-code-plugin)
covers existing entries, organization switches and the Claude Code manual fallback.

### Update / troubleshoot

- Skills refresh automatically once per session. To force it: `claude plugin update appstrate@appstrate`.
- The first sync with the MCP-capable CLI changes the plugin hash even if no skill changed. Exit the active Claude Code session, start a new one, then check `/mcp`; authentication and approval prompts depend on your client version and saved state.
- After `appstrate org switch <id-or-slug>` or connecting the default CLI profile to another instance, run `claude plugin update appstrate@appstrate`, then exit the active Claude Code session and start a new one. Check the new endpoint in `/mcp` and finish OAuth if requested before running an operation. Do not rely on `/reload-plugins` alone: the active connection can retain the previous endpoint. A one-off sync with `--profile` is replaced by the default profile on the next marketplace refresh.
- If the sync fails (not logged in, no space pinned, expired session), the error shows in `/plugin` → Errors. Run `appstrate skills sync` in a terminal to see the full message.
- Setting `CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC` disables the background refresh; updates then only happen through `claude plugin update`.
- Organizations that block command-sourced plugins (`disableCommandPluginSources`) can sync into `~/.claude/skills/` instead: `appstrate skills sync --target claude-user`, e.g. from a cron or launchd entry.

## License

Apache-2.0
