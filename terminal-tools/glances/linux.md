# Glances — Linux Setup

> An eye on your system — a cross-platform monitoring tool that shows CPU, memory, disk I/O, network, processes, Docker containers, and more in a single terminal view. Supports web UI and API modes.

| | |
|---|---|
| **Official Docs** | [glances.readthedocs.io](https://glances.readthedocs.io/) |
| **GitHub** | [github.com/nicolargo/glances](https://github.com/nicolargo/glances) |
| **Version Check** | `glances --version` |

---

## ⚡ Quick Install (One Command)

```bash
# pip (recommended — gets latest version with all features):
pip3 install glances

# or apt (Ubuntu/Debian — may be an older version):
sudo apt install -y glances
```

---

## 📋 One-Shot Full Setup (Copy & Paste)

```bash
#!/usr/bin/env bash
set -e

echo "📦 Installing Glances on Linux..."

# Install Python pip if needed
if ! command -v pip3 &>/dev/null; then
  if command -v apt &>/dev/null; then
    sudo apt update && sudo apt install -y python3-pip python3-dev
  elif command -v dnf &>/dev/null; then
    sudo dnf install -y python3-pip python3-devel
  fi
fi

# Install Glances with optional extras
if command -v pip3 &>/dev/null; then
  pip3 install --upgrade glances

  # Optional Docker monitoring plugin
  pip3 install glances[docker] 2>/dev/null && echo "✅ Docker plugin installed" || true

  # Optional web UI dependencies
  pip3 install glances[web] 2>/dev/null && echo "✅ Web UI plugin installed" || true
elif command -v apt &>/dev/null; then
  sudo apt update && sudo apt install -y glances
elif command -v dnf &>/dev/null; then
  sudo dnf install -y glances
elif command -v snap &>/dev/null; then
  sudo snap install glances
else
  echo "❌ No supported package manager found. Install pip3 and retry."
  exit 1
fi

# Add shell aliases
for SHELL_RC in ~/.bashrc ~/.zshrc; do
  if [[ -f "$SHELL_RC" ]] && ! grep -q 'alias gw=' "$SHELL_RC"; then
    cat >> "$SHELL_RC" << 'EOF'

# Glances
alias gw="glances -w"    # web mode: http://localhost:61208
EOF
  fi
done

source ~/.bashrc 2>/dev/null || true

glances --version
echo ""
echo "✅ Glances installed!"
echo "   Terminal:  glances"
echo "   Web UI:    glances -w  →  http://localhost:61208"
echo "   Remote:    glances -s  (server)  /  glances -c <ip>  (client)"
```

---

## 🚀 Usage Modes

```bash
# Standard terminal view
glances

# Web UI
glances -w                              # http://localhost:61208
glances -w --port 8080                  # custom port
glances -w --password                   # set a password for web UI

# Server / Client (monitor remote machines)
glances -s                              # start server
glances -c <remote-ip>                  # connect as client

# REST API only
glances -w --disable-webui

# Export metrics
glances --export csv --export-csv-file /tmp/glances.csv
glances --export influxdb               # InfluxDB integration
```

---

## 🔧 Configuration

Config file location:
- Linux: `~/.config/glances/glances.conf`
- System-wide: `/etc/glances/glances.conf`

```ini
[global]
refresh=2

[cpu]
critical=95
warning=70

[mem]
critical=95
warning=70

[diskio]
hide=loop.*

[network]
hide=lo
```

---

## 📚 Keyboard Shortcuts

| Key | Action |
|---|---|
| `q` / `Esc` | quit |
| `h` | help |
| `1` | per-CPU stats |
| `C` | sort by CPU |
| `M` | sort by memory |
| `U` | sort by user |
| `P` | sort by PID |
| `A` | auto-sort |
| `d` | toggle disk I/O |
| `f` | toggle filesystem |
| `n` | toggle network |
| `s` | toggle sensors |
| `t` | toggle Docker/containers |
| `z` | kill process |
| `/` | filter processes |
| `e` | extended process info |
