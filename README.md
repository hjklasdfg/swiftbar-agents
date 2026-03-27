# 🦞 SwiftBar Agent Monitor

Real-time OpenClaw agent observability in the macOS menu bar.

![macOS](https://img.shields.io/badge/macOS-only-blue) ![SwiftBar](https://img.shields.io/badge/SwiftBar-v2.0+-green)

## What it shows

**Menu bar**: Emoji + context length of most recently active agent (e.g. `🔧 140k`)

**Dropdown**:
- Agent name, context usage (tokens/limit + %), model alias, last active time
- `▶` running · `—` idle · `✖` failed
- `🫠` context over 100k warning

## Install

### Quick install (OpenClaw on this Mac)

```bash
bash scripts/install.sh
```

### Remote install (OpenClaw on another machine)

```bash
bash scripts/install.sh --remote user@host
```

#### Prerequisites for remote mode

- **Network**: Your Mac and the OpenClaw host must be reachable over the network. If they're not on the same LAN, use a mesh VPN like [Tailscale](https://tailscale.com), [ZeroTier](https://zerotier.com), or [WireGuard](https://wireguard.com) to connect them.
- **SSH key auth**: Passwordless SSH must be configured (`BatchMode=yes`). If not set up yet:
  ```bash
  ssh-keygen -t ed25519 -N ""
  ssh-copy-id user@host
  ```
- **Python 3**: Must be available on the OpenClaw host.

### Via OpenClaw skill

```bash
openclaw skills install swiftbar-agents
```

Then ask your agent: *"Set up agent menu bar monitoring"*

## How it works

```
MacBook (SwiftBar plugin) → SSH → OpenClaw host (status collector) → sessions.json
```

Two components:
1. **`openclaw-status.py`** — Runs on the OpenClaw host, reads agent session data
2. **`swiftbar-plugin.sh`** — Runs on your Mac via SwiftBar, renders the menu bar

## Agent emoji

Reads from each agent's `IDENTITY.md` (`- **Emoji:** 🔧`). Falls back to agent name.

## Model display

Shows aliases: opus, sonnet, haiku, flash, pro. User-configured `modelAliases` take priority.

## Customization

- **Refresh interval**: Rename plugin — `30s` → `10s`, `1m`, `5m`
- **Warning threshold**: Edit `WARN = 100000` in the plugin
- **SSH target**: Set `OPENCLAW_SSH_TARGET` env var or edit `MINI=` in plugin

## Troubleshooting

| Menu bar | Meaning |
|---|---|
| `🦞 ❌` | SSH or local connection failed |
| `🦞 ⚠️` | Data parse error |
| Agent missing | Agent has no session yet |

## License

MIT
