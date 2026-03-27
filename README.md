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

### Via OpenClaw skill (recommended)

```bash
openclaw skills install swiftbar-agents
```

Then either run the installer directly:

```bash
bash ~/.openclaw/skills/swiftbar-agents/scripts/install.sh           # local
bash ~/.openclaw/skills/swiftbar-agents/scripts/install.sh --remote user@host  # remote
```

Or ask your agent to set up menu bar monitoring (any phrasing works).

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

---

## 中文说明

### 功能

在 macOS 菜单栏实时显示 OpenClaw agent 状态。

**菜单栏**：显示最近活跃 agent 的 emoji + 上下文长度（如 `🔧 140k`）

**下拉菜单**：
- Agent 名称、上下文用量（tokens/上限 + 百分比）、模型别名、最后活跃时间
- `▶` 运行中 · `—` 空闲 · `✖` 失败
- `🫠` 上下文超过 100k 警告

### 安装

#### 通过 OpenClaw skill 安装（推荐）

```bash
openclaw skills install swiftbar-agents
```

然后直接运行安装脚本：

```bash
bash ~/.openclaw/skills/swiftbar-agents/scripts/install.sh           # 本地模式
bash ~/.openclaw/skills/swiftbar-agents/scripts/install.sh --remote user@host  # 远程模式
```

也可以直接让 agent 帮你设置，随便怎么说都行，比如"帮我设置菜单栏监控"。

#### 快速安装（OpenClaw 在本机）

```bash
bash scripts/install.sh
```

#### 远程安装（OpenClaw 在另一台机器上）

```bash
bash scripts/install.sh --remote user@host
```

##### 远程模式前置条件

- **网络**：Mac 和 OpenClaw 主机必须网络互通。如果不在同一局域网，推荐使用 [Tailscale](https://tailscale.com)、[ZeroTier](https://zerotier.com) 或 [WireGuard](https://wireguard.com) 组网。
- **SSH 免密登录**：需要配置 SSH 密钥认证（`BatchMode=yes`）。如果还没配置：
  ```bash
  ssh-keygen -t ed25519 -N ""
  ssh-copy-id user@host
  ```
- **Python 3**：OpenClaw 主机上需要安装 Python 3。

### 自定义

- **刷新间隔**：重命名插件文件后缀 — `30s` → `10s`、`1m`、`5m`
- **警告阈值**：修改插件中的 `WARN = 100000`
- **SSH 目标**：设置环境变量 `OPENCLAW_SSH_TARGET` 或修改插件中的 `MINI=`

### 故障排查

| 菜单栏显示 | 含义 |
|---|---|
| `🦞 ❌` | SSH 或本地连接失败 |
| `🦞 ⚠️` | 数据解析错误 |
| Agent 不显示 | 该 agent 尚未产生 session |

## License

MIT
