# 🦞 Context Monitor

Monitor OpenClaw agent context length from the macOS menu bar — no more worrying about context overflow draining your model quota.

![macOS](https://img.shields.io/badge/macOS-only-blue) ![SwiftBar](https://img.shields.io/badge/SwiftBar-v2.0+-green)

[中文说明](#中文说明)

## Features

- **At-a-glance context usage** — menu bar shows the active agent (emoji if set, otherwise agent ID) and its context length, e.g. `🔧 140k`
- **Multi-agent overview** — dropdown lists all agents with context length, model, and status
- **Status indicators** — `▶` running / `—` idle / `✖` failed. Shows `🫠` when context exceeds 100k
- **Agent display** — reads each agent's custom emoji from `IDENTITY.md`
- **Simplified model names** — shows short aliases (opus, sonnet, haiku, flash, pro) instead of full model IDs
- **Local + remote** — works whether OpenClaw runs on your Mac or a remote server

## Install

### Option A: Via OpenClaw skill (recommended)

```bash
openclaw skills install context-monitor
```

Then set up via either method:

**A1. Run the installer:**

```bash
bash ~/.openclaw/skills/context-monitor/scripts/install.sh           # local
bash ~/.openclaw/skills/context-monitor/scripts/install.sh --remote user@host  # remote
```

**A2. Or ask your agent**, e.g. *"set up menu bar monitoring"* (any phrasing works).

### Option B: Manual install

**Local** (OpenClaw on this Mac):

```bash
bash scripts/install.sh
```

**Remote** (OpenClaw on another machine):

```bash
bash scripts/install.sh --remote user@host
```

> **Remote mode prerequisites**
>
> - **Network**: Your Mac and the OpenClaw host must be reachable. If not on the same LAN, use [Tailscale](https://tailscale.com), [ZeroTier](https://zerotier.com), or [WireGuard](https://wireguard.com).
> - **SSH key auth**: Passwordless SSH required (`BatchMode=yes`). Setup:
>   ```bash
>   ssh-keygen -t ed25519 -N ""
>   ssh-copy-id user@host
>   ```
> - **Python 3**: Must be available on the OpenClaw host.

## How it works

```
MacBook (SwiftBar plugin) → SSH → OpenClaw host (status collector) → sessions.json
```

Two components:
1. **`openclaw-status.py`** — Runs on the OpenClaw host, reads agent session data
2. **`swiftbar-plugin.sh`** — Runs on your Mac via SwiftBar, renders the menu bar

## Customization

Plugin file: `~/Library/Application Support/SwiftBar/Plugins/context-monitor.30s.sh`

- **Refresh interval** — rename the file suffix, e.g. `30s` → `10s`, `1m`, `5m`:
  ```bash
  mv ~/Library/Application\ Support/SwiftBar/Plugins/context-monitor.{30s,10s}.sh
  ```
- **Warning threshold** — edit the above plugin file, change `WARN_THRESHOLD = 100000` to your preferred token count
- **SSH target** — edit `MINI="user@host"` in the above plugin file, or set an env var:
  ```bash
  export OPENCLAW_SSH_TARGET="user@new-host"
  ```

## Troubleshooting

| Menu bar | Meaning |
|---|---|
| `🦞 ❌` | SSH or local connection failed |
| `🦞 ⚠️` | Data parse error |
| Agent missing | Agent has no session yet |

---

## 中文说明

在菜单栏就可以查看当前 Agent 上下文长度，不用再担心上下文过长导致模型配额耗尽。

### 功能一览

- **一眼掌握上下文用量** — 菜单栏显示当前活跃 agent（优先显示 emoji，如无设置会显示 Agent ID），以及对应 Agent 的上下文长度。效果：`🔧 140k`
- **多 Agent 总览** — 下拉菜单展示所有 agent 的上下文长度、模型、运行状态
- **状态标识** — `▶` 运行中 / `—` 空闲 / `✖` 失败。上下文超过 100k 时会显示标志 `🫠`
- **Agent 显示** — 从 `IDENTITY.md` 读取每个 agent 的自定义 emoji
- **Agent 模型显示简化** — 显示简称（opus、sonnet、haiku、flash、pro）而非完整模型 ID
- **本地 + 远程** — 支持 OpenClaw 在本机或远程服务器上运行

### 安装

#### 方式一：通过 OpenClaw skill 安装（推荐）

```bash
openclaw skills install context-monitor
```

然后选择以下任一方式完成设置：

**a. 运行安装脚本：**

```bash
bash ~/.openclaw/skills/context-monitor/scripts/install.sh           # 本地模式
bash ~/.openclaw/skills/context-monitor/scripts/install.sh --remote user@host  # 远程模式
```

**b. 或者让 agent 帮你设置**，比如说"帮我设置菜单栏监控"（任意表述均可）。

#### 方式二：手动安装

**本地**（OpenClaw 在本机）：

```bash
bash scripts/install.sh
```

**远程**（OpenClaw 在另一台机器上）：

```bash
bash scripts/install.sh --remote user@host
```

> **远程模式前置条件**
>
> - **网络** — Mac 和 OpenClaw 主机必须网络互通。不在同一局域网的话，推荐用 [Tailscale](https://tailscale.com)、[ZeroTier](https://zerotier.com) 或 [WireGuard](https://wireguard.com) 组网。
> - **SSH 免密登录** — 需要配置密钥认证（`BatchMode=yes`）：
>   ```bash
>   ssh-keygen -t ed25519 -N ""
>   ssh-copy-id user@host
>   ```
> - **Python 3** — OpenClaw 主机上需要安装。

### 自定义

插件文件位于 `~/Library/Application Support/SwiftBar/Plugins/context-monitor.30s.sh`

- **刷新间隔** — 重命名文件后缀即可，例如把 `30s` 改为 `10s`、`1m` 或 `5m`：
  ```bash
  mv ~/Library/Application\ Support/SwiftBar/Plugins/context-monitor.{30s,10s}.sh
  ```
- **警告阈值** — 编辑上述插件文件，找到 `WARN_THRESHOLD = 100000`，改为你想要的 token 数
- **SSH 目标** — 如果远程主机地址变了，编辑上述插件文件中的 `MINI="user@host"`，或设置环境变量：
  ```bash
  export OPENCLAW_SSH_TARGET="user@new-host"
  ```

### 故障排查

| 菜单栏显示 | 含义 |
|---|---|
| `🦞 ❌` | SSH 或本地连接失败 |
| `🦞 ⚠️` | 数据解析错误 |
| Agent 不显示 | 该 agent 尚未产生 session |

## License

MIT
