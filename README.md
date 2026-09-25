# Appstrate plugins for Claude Code

Official [Claude Code](https://code.claude.com) plugin marketplace for [Appstrate](https://appstrate.dev).

## `appstrate` — your organization's skills, synced every session

Installs the skills of your Appstrate organization (the org and space pinned on your `appstrate` CLI profile) as `/appstrate:<skill>` commands, and keeps them in sync: Claude Code re-runs the sync in the background once per session and reloads the plugin when a skill was published, updated or removed. The same run also refreshes `~/.agents/skills/` for OpenAI Codex.

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

Claude Code shows the exact command it will run (`appstrate code sync --target claude-plugin --target codex --print-path`) and asks you to accept it once. Only published skill versions are synced; publish a version from the Appstrate dashboard to ship a change.

### Update / troubleshoot

- Skills refresh automatically once per session. To force it: `claude plugin update appstrate@appstrate`.
- When the marketplace changes the command it runs (as when `appstrate packages sync` became `appstrate code sync`), Claude Code stops re-running it in the background until you accept the new one: run `claude plugin update appstrate@appstrate` once. The new command needs an `appstrate` CLI that knows it — `appstrate self-update` (or `npm i -g appstrate@latest`).
- If the sync fails (not logged in, no space pinned, expired session), the error shows in `/plugin` → Errors. Run `appstrate code sync --target claude-plugin` in a terminal to see the full message.
- Setting `CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC` disables the background refresh; updates then only happen through `claude plugin update`.
- Organizations that block command-sourced plugins (`disableCommandPluginSources`) can sync into `~/.claude/skills/` instead: `appstrate code sync --target claude-user`, e.g. from a cron or launchd entry.

## License

Apache-2.0
