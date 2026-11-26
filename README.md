# Brave + Lumo Setup

Lumo browser automation works with Chrome only. This guide shows how to make Lumo Desktop control Brave instead. Setup takes about 15 minutes.

After setup, Lumo can:

- open tabs in your Brave browser
- read pages
- click
- fill forms

The browser stays signed in. The tool does not copy your profile. It installs no extensions. It exports no passwords.

The tool is [brave-session-mcp](https://github.com/MacStenk/brave-mcp) by MacStenk. It is an unofficial community project under an MIT license. It is not a Brave Software product and not a Proton product. I verified this setup on macOS in November 2026.

## What You Need

- Lumo Desktop, signed in. Connectors run in the desktop app only. They do not run in the web app.
- Node.js 22 or newer. Check your version: `node -v`
- Brave Nightly. Download it [here](https://brave.com/download-nightly). The server is tested against Nightly. Regular Brave is untested.

## Procedure

### Step 1. Enable Remote Debugging

1. Open Brave Nightly.
2. Type `brave://inspect/#remote-debugging` in the address bar.
3. Enable the permission.

When Lumo connects the first time, Brave shows a confirmation dialog. Click Approve. After each browser restart, Brave asks again. Click Approve once more. The connection then restores itself.

### Step 2. Install the Server

1. Install the package:

```bash
npm install -g brave-session-mcp
```

2. Find the install path:

```bash
which brave-session-mcp
```

3. Copy the path it prints. On my Mac the path was `/opt/homebrew/bin/brave-session-mcp`. Your path can differ.

4. Run the path in your terminal. The server starts and waits. It shows no window. This is correct behavior.

5. Press Ctrl+C to stop it.

### Step 3. Add the Connector

1. Open Lumo Desktop.
2. Click Customize, then Connectors.
3. Click Add MCP server.
4. Enter these values:

| Field | Value |
| --- | --- |
| ID | `brave-session` |
| Display Name | `Brave Session` |
| Description | `Controls the local Brave browser` |
| Command | The path from step 2 |
| Arguments | Empty |
| Environment | Empty |
| Secret Environment | Empty |

5. Click Connect.
6. Click Close.
7. Click Save.

No API key is needed. The server attaches to the browser you already use. There is nothing to enter in the secret fields.

### Step 4. Test the Setup

1. Open a chat with your assistant.
2. Say: "Open proton.me in Brave."
3. If a new tab opens, the setup works.

## Problems and Corrections

| Symptom | Cause | Correction |
| --- | --- | --- |
| Could not reach the MCP server. No response within 30s. | First run of npx downloads the package and exceeds the connect timeout. | Use the global install. Put the absolute path in the Command field. Leave Arguments empty. |
| The connector is not in the list after you close the dialog. | Lumo discarded the entry. | Enter the values again. Click Save. |
| Message: Environment lines must be in KEY=VALUE form. | Wrong environment format. | Leave Environment empty. If you need a variable, enter it as `NAME=value`, one per line. |
| The connector connects, but the browser count is zero. | Brave is closed, or debugging is off. | Start Brave Nightly. Check the setting in step 1. Approve the dialog. |
| After a browser restart, nothing happens. | The approval lapsed. | Approve the dialog in Brave once. |

## Before You Let It Near Anything Important

Caution: The assistant operates your signed-in browser. It can reach every site where you are logged in. Set limits before you point it at work sites or accounts with money in them.

**Set tool permissions in Lumo.** Go to Settings, Connectors, Brave Session, Functions. Set each tool to auto-approve, ask first, or never run. Ask-first is a good setting until you trust the setup.

**Block domains in the server.** The server reads `~/.config/brave-mcp/settings.json` before every action. Set a `blocked` list of domains the assistant must never open. Set an agent `mode` there too. If the settings file is broken, every tool fails. The server does not fall back to allowing everything. Good design.

## If It Stops Working

Do these checks in order:

1. Run `node -v`. Confirm Node is installed and version 22 or newer.
2. Run `which brave-session-mcp`. Confirm the path exists. Run it. Confirm it starts and waits.
3. Confirm Brave Nightly is open and debugging is enabled. Approve the dialog if needed.
4. Open Customize, Connectors. Confirm the connector is connected.
5. Confirm the Command field holds the absolute path, not npx.
6. Ask the assistant to list your open Brave tabs. This test exercises the full chain. The failure point tells you the cause.

## Credits

- Tool: [brave-session-mcp](https://github.com/MacStenk/brave-mcp) / [MacStenk/brave-mcp](https://github.com/MacStenk/brave-mcp). MIT license.
- I am a Lumo user. I have no affiliation with Proton or Brave.
- If Proton ships native Brave support, you can delete all of this. I will be happy about it.

Questions or corrections: open an issue.
