# Glances — macOS Setup

> An eye on your system — a cross-platform monitoring tool that shows CPU, memory, disk I/O, network, processes, Docker containers, and more in a single terminal view. Supports web UI and API modes.

| | |
|---|---|
| **Official Docs** | [glances.readthedocs.io](https://glances.readthedocs.io/) |
| **GitHub** | [github.com/nicolargo/glances](https://github.com/nicolargo/glances) |
| **Version Check** | `glances --version` |

---

## ⚡ Quick Install (One Command)

```bash
brew install glances
```

---

## 📋 One-Shot Full Setup (Copy & Paste)

```bash
#!/usr/bin/env bash
set -e

echo "📦 Installing Glances on macOS..."

if ! command -v brew &>/dev/null; then
  /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
fi

brew install glances

# Optional: install Docker plugin support (monitors containers)
pip3 install glances[docker] 2>/dev/null || brew install python && pip3 install glances[docker]

# Add shell aliases for common modes
if ! grep -q 'alias glances' ~/.zshrc 2>/dev/null; then
  cat >> ~/.zshrc << 'EOF'

# Glances — system monitoring
alias gw="glances -w"           # web server mode (http://localhost:61208)
alias gd="glances --disable-plugin docker"  # no docker overhead
EOF
fi

source ~/.zshrc 2>/dev/null || true

glances --version
echo ""
echo "✅ Glances installed!"
echo "   Terminal:  glances"
echo "   Web UI:    glances -w  →  http://localhost:61208"
echo "   REST API:  glances -w --disable-webui"
```

---

## 🚀 Usage Modes

```bash
# Standard terminal view
glances

# Web UI (access at http://localhost:61208)
glances -w

# Web UI on custom port
glances -w --port 8080

# REST API mode (no UI)
glances -w --disable-webui

# Monitor a remote machine (client mode)
glances -c <remote-ip>

# Remote server mode
glances -s

# Export metrics to CSV
glances --export csv --export-csv-file /tmp/glances.csv
```

---

## 🔧 Configuration

Config file location:
- macOS: `~/.config/glances/glances.conf`

```ini
[global]
# Refresh interval in seconds
refresh=2

[cpu]
critical=95
warning=70

[mem]
critical=95
warning=70

[diskio]
# Hide specific disks
hide=loop.*

[network]
hide=lo
```

---

## 📚 Keyboard Shortcuts

| Key | Action |
|---|---|
| `q` / `Esc` | quit |
| `h` | show / hide help |
| `1` | per-CPU stats |
| `2` | NUMA stats |
| `3` | per-GPU stats |
| `A` | auto mode (sort by most used resource) |
| `C` | sort processes by CPU |
| `M` | sort by memory |
| `U` | sort by user |
| `I` | sort by I/O |
| `P` | sort by PID |
| `T` | sort by CPU time |
| `a` | sort automatically |
| `d` | toggle disk I/O panel |
| `f` | toggle file system panel |
| `g` | generate graphs |
| `i` | toggle IRQ panel |
| `k` | toggle connections panel |
| `l` | toggle log panel |
| `n` | toggle network panel |
| `p` | toggle ports panel |
| `s` | toggle sensors panel |
| `t` | toggle containers panel |
| `w` | delete old warnings from log |
| `z` | kill selected process |
| `/` | filter process list |
| `e` | extended process info |
| `Enter` | filter by process |
| `F5` | refresh stats |
