---
name: swiftbar-agents
description: macOS menu bar agent observability via SwiftBar. Shows OpenClaw agent context usage, model, running state, and health in the menu bar. Includes one-command installer that auto-detects local vs remote OpenClaw setup. Use when setting up agent monitoring on macOS, installing SwiftBar status plugin, or when asked about agent dashboard/observability/monitoring on Mac. Triggers on "menu bar", "SwiftBar", "agent status", "agent monitor", "observability", "dashboard mac", "agent context".
---

# SwiftBar Agent Monitor

Real-time OpenClaw agent observability in the macOS menu bar.

## Install

Two modes depending on where OpenClaw runs:

### Local mode (OpenClaw on the same Mac)

Agent can run the installer directly:

```bash
bash scripts/install.sh
```

### Remote mode (OpenClaw on another machine)

Agent CANNOT install SwiftBar on the user's Mac remotely. Follow this flow:

**Step 0: Verify prerequisites.** Remote mode requires:
- The user's Mac and OpenClaw host must be network-reachable (same LAN, or connected via Tailscale/ZeroTier/WireGuard)
- SSH key auth configured (passwordless, `BatchMode=yes`)
- Python 3 on the OpenClaw host

If the user is unsure about network connectivity, suggest Tailscale as the simplest option.

**Step 1: Ask the user:**

> "Does OpenClaw run on this Mac, or on a remote machine?
> If remote, what's the SSH address? (e.g. `user@hostname` — the two machines need to be network-reachable, e.g. same LAN or via Tailscale)"

**Step 2: Agent deploys the status collector** to the OpenClaw host (agent can do this directly):

```bash
scp scripts/openclaw-status.py <user>@<host>:~/.openclaw/openclaw-status.py
```

**Step 3: Agent generates copy-paste instructions** for the user to run on their own Mac:

```
# 1. Install SwiftBar (skip if already installed)
brew install --cask swiftbar

# 2. Create plugin directory
mkdir -p ~/Library/Application\ Support/SwiftBar/Plugins

# 3. Save this plugin file (agent generates with correct SSH target)
cat > ~/Library/Application\ Support/SwiftBar/Plugins/openclaw-agents.30s.sh << 'EOF'
(agent inserts swiftbar-plugin.sh content with MINI= set to user's SSH target)
EOF
chmod +x ~/Library/Application\ Support/SwiftBar/Plugins/openclaw-agents.30s.sh

# 4. Open SwiftBar
open /Applications/SwiftBar.app
# If first launch, select plugin folder: ~/Library/Application Support/SwiftBar/Plugins
```

**Important**: Present all commands as a single copy-paste block. Ensure SSH key auth is set up between the user's Mac and the OpenClaw host beforehand.

## What it shows

**Menu bar**: Emoji + context length of most recently active agent (e.g. `🔧 140k`)

**Dropdown**:
- Agent name, context usage (tokens/limit + %), model alias, last active time
- `▶` running · `—` idle · `✖` failed
- `🫠` context over 100k (approaching limits)

## Agent emoji

Read from each agent's `IDENTITY.md` (`- **Emoji:** 🔧`). Falls back to agent name if not set.

## Model display

Shows model aliases (opus, sonnet, haiku, flash, pro). Falls back to model name without provider prefix.

## Files

- `scripts/install.sh` — One-command installer
- `scripts/openclaw-status.py` — Status collector (runs on OpenClaw host)
- `scripts/swiftbar-plugin.sh` — SwiftBar plugin template (remote mode)

## Customization

- **Refresh interval**: Rename plugin file suffix (`30s` → `10s`, `1m`, `5m`)
- **Warning threshold**: Edit `WARN = 100000` in plugin
- **SSH target**: Edit `MINI=` in plugin or set `OPENCLAW_SSH_TARGET` env var

## Troubleshooting

- `🦞 ❌` — SSH or local read failed. Check connection.
- `🦞 ⚠️` — Data parse error. Run `python3 ~/.openclaw/openclaw-status.py` on host.
- Agent missing — No `sessions.json` yet (agent never used).
