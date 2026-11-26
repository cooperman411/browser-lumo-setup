# Brave + Lumo Setup

Lumo's browser automation only works with Chrome right now. If you use Brave, this guide fixes that today using a community MCP server. It takes about fifteen minutes.

The result: Lumo Desktop can open tabs, read pages, click, and fill forms in your actual running Brave session. Your logins stay in the browser. No profile copying, no extensions, no exported passwords.

This is a setup guide, not a tool I wrote. The tool is [brave-session-mcp](https://github.com/MacStenk/brave-mcp) by MacStenk (MIT license, unofficial, not affiliated with Brave Software). Everything here is the exact setup I verified end to end on macOS in November 2026.

## What You Need

- Lumo Desktop, signed in (connectors only run in the desktop app, not the web app)
- Node.js 22 or newer (`node -v` to check)
- Brave Nightly, the server's tested target ([download](https://brave.com/download-nightly))

Regular Brave may work but is untested. See the brave-session-mcp README for the `BRAVE_APP` and `BRAVE_USER_DATA_DIR` settings if you want to try it.

## The Steps

### 1. Enable remote debugging in Brave, once

Open `brave://inspect/#remote-debugging` in Brave Nightly and enable the permission. The first time Lumo connects, Brave shows a confirmation dialog. Approve it. After a Brave restart you re-approve once and the connection comes back on its own.

### 2. Install the server

```bash
npm install -g brave-session-mcp
```

Then find where it landed:

```bash
which brave-session-mcp
```

Copy the path it prints. On my Mac it was `/opt/homebrew/bin/brave-session-mcp`. Yours may differ; that's fine.

Run the path once in your terminal. It should start and sit silently waiting. That's correct, it has no window. Press Ctrl+C to quit.

### 3. Add the connector in Lumo Desktop

Open Customize, then Connectors, then Add MCP server. Fill in:

| Field | Value |
| --- | --- |
| ID | `brave-session` |
| Display Name | `Brave Session` |
| Description | Drives the local Brave browser |
| Command | The absolute path from step 2 |
| Arguments | Leave empty |
| Environment | Leave empty |
| Secret Environment | Leave empty |

Click **Save**, then **Connect**.

No API keys or passwords go anywhere. The server attaches to the browser you're already signed into, so there's nothing to put in the secret fields.

### 4. Test it

Ask your assistant to open a page, something like "open proton.me in Brave." If a new tab appears, you're done.

## Things That Cost Me Time, So They Don't Cost You Any

**Don't use npx as the command.** My first attempt used `npx -y brave-session-mcp`. The first run downloads the package, which blew past Lumo's 30-second connect timeout ("could not reach the MCP server... no response within 30s"). The global install plus the absolute path avoids the download entirely.

**Save, don't close.** Lumo's connector dialog discards the whole entry if you close the window instead of clicking Save. Ask me how I know.

**The browser restart gotcha.** Brave asks you to re-approve remote debugging after each restart. One click, then it reconnects on the next use.

**Environment fields want `KEY=VALUE` lines.** If you ever add an environment variable (you probably won't need one), it goes in as `BRAVE_MCP_PROFILE=Agent`, one per line. Name and value boxes or colon syntax get rejected with "Environment lines must be in KEY=VALUE form."

## Before You Let It Near Anything Important

The agent operates your signed-in browser. It sees everything you see, including every site you're logged into. Two controls before you point it at work sites or anything with money in it:

**Per-tool permissions in Lumo.** In Settings, Connectors, Brave Session, Functions, you can set each tool (open, click, fill, upload) to auto-approve, ask first, or never run. Ask-first is a reasonable default until you trust it.

**Domain blocking in brave-session-mcp.** The server reads `~/.config/brave-mcp/settings.json` on every action. You can set a `blocked` list of domains the agent can never touch, plus an agent `mode`. A malformed settings file fails closed: every tool errors rather than quietly allowing everything. Good design.

## If It Breaks Later

Work down this list:

1. `node -v` confirms Node is still installed and 22 or newer.
2. `which brave-session-mcp` confirms the binary still exists. Running it directly should still start and wait silently.
3. Brave Nightly is running and remote debugging is enabled. If it restarted, re-approve the dialog.
4. The connector still shows as connected in Lumo, Customize, Connectors.
5. The Command field still holds the absolute path, not npx.
6. Ask the assistant to list your open Brave tabs. That exercises the whole chain and tells you where it failed.

## Credits and Caveats

- The tool is [brave-session-mcp](https://github.com/MacStenk/brave-mcp) / [MacStenk/brave-mcp](https://github.com/MacStenk/brave-mcp), MIT license. It's a community project, not a Brave Software product and not a Proton product.
- I'm a Lumo user, not affiliated with Proton or Brave.
- If Proton ships native Brave support, you can delete all of this. I'll be happy about it.

Questions or corrections, open an issue.
