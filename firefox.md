# Firefox + Lumo Setup

Lumo browser automation works with Chrome only. This guide shows how to make Lumo Desktop control Firefox instead. Setup takes about ten minutes.

After setup, Lumo can:

- open tabs in Firefox
- read pages
- click
- fill forms

The tool is Firefox MCP from Mozilla. Documentation lives [here](https://firefox-source-docs.mozilla.org/ai-agent-tools/firefox-mcp.html) and [here](https://github.com/mozilla/firefox-mcp). The package is `@mozilla/firefox-mcp` on npm.

## How This Differs From the Brave Guide

The Brave setup attaches Lumo to the browser you already use. Your logins stay in place.

Firefox MCP starts its own Firefox instance. By default, that instance runs on a temporary profile. Your logins do not carry over. This is safer, and it is also the correct default: the assistant gets a clean browser, not your signed-in one.

You can point the tool at a specific Firefox build or a profile directory with options. See the options table below. Mozilla's own security guidance is blunt: do not run this server against your regular profile. The agent can reach everything the browser can reach.

## What You Need

- Lumo Desktop, signed in. Connectors run in the desktop app only.
- Node.js 20 or newer. Check your version: `node -v`
- Firefox installed. The tool finds it automatically. If you have more than one, you can name the exact one with an option.

## Procedure

### Step 1. Warm the npm Cache

Firefox MCP is run through npx. The first run downloads the package. That download can exceed Lumo's 30-second connect timeout, the same problem I hit with the Brave setup.

Prevent it. Run this once in your terminal and let the download finish:

```bash
npx -y @mozilla/firefox-mcp@latest
```

The server starts and waits. Press Ctrl+C to stop it. The package is now cached, so Lumo will not have to download it.

### Step 2. Find Your npx Path

Lumo runs as a desktop app and may not see your PATH. Give it the absolute path:

```bash
which npx
```

Copy the result. On my Mac it was `/opt/homebrew/bin/npx`.

### Step 3. Add the Connector

1. Open Lumo Desktop.
2. Click Customize, then Connectors.
3. Click Add MCP server.
4. Enter these values:

| Field | Value |
| --- | --- |
| ID | `firefox-session` |
| Display Name | `Firefox Session` |
| Description | `Controls a local Firefox instance` |
| Command | The npx path from step 2 |
| Arguments | `-y @mozilla/firefox-mcp@latest` |
| Environment | Empty |
| Secret Environment | Empty |

5. Click Save.
6. Click Connect.

Caution: If you close the window instead of clicking Save, Lumo discards the entry.

### Step 4. Test the Setup

1. Open a chat with your assistant.
2. Say: "Open proton.me in Firefox."
3. A Firefox window opens and loads the page. The setup works.

## Options You May Want

Put these in the Arguments field after the package name:

| Option | What It Does |
| --- | --- |
| `--headless` | Runs Firefox with no visible window |
| `--viewport 1280x720` | Sets the window size |
| `--start-url about:blank` | Sets the page that opens at start |
| `--firefox-path /path/to/firefox` | Uses a specific Firefox build |
| `--profile-path /path/to/dir` | Keeps a persistent profile in that directory |

Example Arguments field with two options:

```text
-y @mozilla/firefox-mcp@latest --headless --viewport 1280x720
```

The `--profile-path` option creates a `firefox_mcp_profile` subdirectory and uses it from then on. Use a dedicated directory for this. Do not point it at your real Firefox profile.

## Problems and Corrections

| Symptom | Cause | Correction |
| --- | --- | --- |
| Could not reach the MCP server. No response within 30s. | First run downloads the package. | Do step 1 again and let the download finish. |
| The connector is not in the list. | Lumo discarded the entry. | Enter the values again. Click Save. |
| Connect succeeds, but Firefox does not start. | Node is missing or too old. | Run `node -v`. Install Node 20 or newer. |
| The wrong Firefox opens. | More than one Firefox on the machine. | Add `--firefox-path` with the correct path. |
| Your logins are missing in the new window. | Expected behavior. The server uses a temporary profile. | Log in inside that window if you need to, or see the options above. Do not use your regular profile. |

## Before You Let It Near Anything Important

The assistant controls a live browser. Whatever that browser can reach, the assistant can reach.

Set tool permissions in Lumo. Go to Settings, Connectors, Firefox Session, Functions. Set each tool to auto-approve, ask first, or never run. Ask-first is a good setting until you trust the setup.

Use the temporary profile until you have a reason not to. If you must keep a profile, put it in a dedicated directory, and think hard before you log into banking, email, or work accounts inside it.

## Credits

- Tool: [Firefox MCP](https://github.com/mozilla/firefox-mcp) from Mozilla. See the [official documentation](https://firefox-source-docs.mozilla.org/ai-agent-tools/firefox-mcp.html).
- The Claude-specific commands in Mozilla's docs (like `claude mcp add`) do not apply to Lumo. Use the connector table above.
- I am a Lumo user. I have no affiliation with Proton or Mozilla.

Questions or corrections: open an issue.
