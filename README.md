# Grok Desktop - OS1 Edition

> **Grok-Powered OS1** · by xAI · powered by Orgo · forked from Hermes Desktop

A native macOS interface for **Grok** (xAI) that lives in a cloud computer.
Inspired by *Her* (2013): warm coral on cream, thin type, calm motion.

Provision a cloud computer, hand it to the agent, and stay in one
focused workspace: sessions, kanban, files, skills, cron jobs, and a
real terminal. The infrastructure is Orgo; the brain is **Grok** from xAI.
The product you touch is Grok Desktop OS1.

## What you get

- **Cloud computers, end to end**: paste your API key once, pick a
  workspace, pick a computer (or create one), save. The app talks
  directly to the platform's HTTP API and the per-VM websocket
  terminal — no SSH, no gateway, no helper service on the VM.
- **One-click agent install** on a fresh computer. The first time you
  open the workspace and the agent isn't there, the Overview screen
  surfaces an "Install Hermes Agent" button. ~60–90 seconds later
  Sessions, Kanban, Files, Skills, and Cron all populate.
- **Real interactive shell** over the per-VM terminal websocket.
  Bytes stream in real time; resize works; output and history reflow
  cleanly.
- **SSH connections still supported** for hosts you reach over SSH
  today. Same flow as the upstream Hermes Desktop fork OS1 was built
  on.
- **Everything else** from the foundation: native Sessions browser
  with full-text search, Kanban board, file editor with conflict
  checks, skills viewer, cron job manager, profile-aware paths,
  English / Simplified Chinese / Russian localization scaffolding.
- **Real-time voice powered by Grok**: Full voice conversation with
  Grok using xAI's realtime voice API. Talk naturally, watch Grok
  control the computer in real time, just like in the demo.

## Requirements

- macOS 14 or newer (Apple Silicon or Intel — universal build)
- One of:
  - An **Orgo account** with an API key (the cloud-computer infra
    powering OS1 — get a key at
    [orgo.ai/settings/api-keys](https://www.orgo.ai/settings/api-keys)),
    OR
  - A host you already reach with `ssh` from this Mac without
    interactive prompts (same flow as upstream Hermes Desktop)

For cloud computers, the app handles VM provisioning, agent
installation, and the websocket terminal automatically. For SSH
connections, the host needs `python3` on the non-interactive SSH PATH
and Hermes already installed.

## Install

Download the latest `OS1.app.zip` from the GitHub Releases page,
unzip it, drag `OS1.app` into `/Applications`, and launch.

The build is universal (Apple Silicon + Intel) and ad-hoc signed.
On first launch macOS may say it can't verify the developer — right-click
the app, choose Open, and confirm.

## Setup

### Cloud computer (recommended)

1. Open the **Connections** tab → click **Add Host**
2. Switch the transport picker to **Orgo VM**
3. Paste your API key → click **Verify & Save**. The key persists in
   the macOS Keychain; subsequent connections reuse it.
4. Pick a workspace from the dropdown.
5. Pick a computer, or click **Create new computer…** to spin one up
   inline (defaults: Linux, 8 GB RAM, 4 CPU, 50 GB disk).
6. Save → the connection is selectable from the host list.
7. If the agent isn't installed on the VM, the **Overview** screen
   shows an install banner. One click runs the official Hermes
   Agent installer. You can use the rest of the app while it runs.

### SSH

Add a connection and switch the transport picker to **SSH**. Alias or
host, optional user/port, optional Hermes profile.

## Build from source

```sh
./scripts/build-macos-app.sh
```

The bundle lands at `dist/OS1.app`.

```sh
swift test
```

## Realtime voice mode (Grok-powered)

**This fork is switched to xAI Grok Voice**.

OS1 now uses **Grok's realtime voice API** (`grok-voice-think-fast-1.0`)
for natural, low-latency voice conversations.

The app starts a loopback session endpoint when the boot animation finishes.
The bottom-left **Voice** row toggles the live voice connection on or off.

**Key changes from original:**
- Voice backend: OpenAI Realtime → **xAI Grok Voice** (WebSocket-based realtime)
- API key: `OPENAI_API_KEY` → **`XAI_API_KEY`** (or set in Providers tab as xAI key)
- Endpoint: `https://api.openai.com/v1/realtime/calls` → `wss://api.x.ai/v1/realtime?model=grok-voice-think-fast-1.0`
- Authentication: Bearer token with your `xai-...` key

Use the **Providers** tab to save your **xAI API key** in the macOS Keychain.
For local development, `XAI_API_KEY` is supported as a fallback.

Run from source with an environment fallback:

```sh
XAI_API_KEY="xai-..." swift run OS1
```

Run the packaged app from a shell:

```sh
./scripts/build-macos-app.sh
XAI_API_KEY="xai-..." ./dist/OS1.app/Contents/MacOS/OS1
```

The voice session still exposes Orgo MCP tools to Grok as function tools,
just like before. Grok's superior reasoning + tool use makes the
computer control experience even better.

Voice mode runs `npx -y @orgo-ai/mcp` by default. You can override the
bridge with the same environment variables as before.

## How it routes

For cloud connections:

1. **HTTP ops** (`/bash`, `/exec`) try the platform proxy at
   `https://www.orgo.ai/api/computers/{id}/...` first. On a 5xx
   that looks like a routing failure (ECONNREFUSED, gateway timeout,
   stale port), the transport falls back to the direct VM URL
   `https://<fly_instance_id>.orgo.dev/...` with the VNC password as
   bearer. Long-running ops (e.g. the agent installer) skip the
   proxy entirely since its 30s request timeout would always trip
   first.
2. **Terminal** opens a websocket directly to
   `wss://<fly_instance_id>.orgo.dev/terminal?token=<vncPassword>`,
   feeding bytes into SwiftTerm.

VM clock drift, missing system git, stale apt locks from earlier
attempts — all handled in the install path so you don't have to wrestle
with the VM by hand.

## Acknowledgements

OS1 builds on two layers of generous prior work:

- The original native macOS application code is from the Hermes Desktop project.
- This Grok-powered fork adds full xAI realtime voice support.

Special thanks to nickvasilescu for the OS1 edition and Orgo for the cloud infra.

---

**Ready to build your own Grok Desktop?** Clone this repo, get an xAI API key from https://console.x.ai, and run the build script.

MIT License · Made for the Grok community