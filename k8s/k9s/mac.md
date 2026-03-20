# k9s — macOS Setup

> A terminal-based UI to interact with your Kubernetes clusters in style. Navigate, inspect, manage resources, view logs, exec into pods — all from a fast keyboard-driven TUI.

| | |
|---|---|
| **Official Docs** | [k9scli.io](https://k9scli.io/) |
| **GitHub** | [github.com/derailed/k9s](https://github.com/derailed/k9s) |
| **Version Check** | `k9s version` |

---

## ⚡ Quick Install (One Command)

```bash
brew install derailed/k9s/k9s
```

---

## 📋 One-Shot Full Setup (Copy & Paste)

```bash
#!/usr/bin/env bash
set -e

echo "📦 Setting up k9s on macOS..."

# ── 1. Install Homebrew if not present ─────────────────────────────────────
if ! command -v brew &>/dev/null; then
  /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
fi

# ── 2. Install k9s ──────────────────────────────────────────────────────────
brew install derailed/k9s/k9s

# ── 3. Set required environment variables ───────────────────────────────────
ZSHRC="$HOME/.zshrc"
if ! grep -q 'KUBE_EDITOR' "$ZSHRC" 2>/dev/null; then
  cat >> "$ZSHRC" << 'EOF'

# ── k9s ──────────────────────────────────────────────────────────────────────
export KUBE_EDITOR="vim"        # editor used when pressing 'e' in k9s
export TERM=xterm-256color      # required for full 256-color support
EOF
  echo "  → Environment variables added to ~/.zshrc"
fi

# ── 4. Verify ─────────────────────────────────────────────────────────────────
k9s version

echo ""
echo "✅ k9s is ready! Run: k9s"
echo "   Tips:"
echo "   • Press '?' in k9s to show all keybindings"
echo "   • Type ':pods' or ':dp' to navigate resources"
echo "   • Press 'l' on a pod to tail logs"
echo "   • Press 's' on a pod to shell into it"
```

---

## 🔧 Configuration

k9s config lives at `~/.config/k9s/` on macOS.

```bash
# View config location
k9s info

# Apply a skin (theme)
# Copy a skin file to ~/.local/share/k9s/skins/
# Then set in config: ui.skin: dracula
```

### Custom `~/.config/k9s/config.yaml`

```yaml
k9s:
  refreshRate: 2
  readOnly: false          # set true to disable edit/delete
  noExitOnCtrlC: false
  ui:
    enableMouse: false
    headless: false
    crumbsless: false
    skin: dracula           # optional skin name
  logger:
    tail: 200
    buffer: 5000
    sinceSeconds: -1        # -1 = stream latest logs
    showTime: false
```

---

## 📚 Keyboard Shortcuts Cheat Sheet

| Key | Action |
|---|---|
| `?` | Show help / all keybindings |
| `:pods` `:dp` `:svc` `:ns` | Navigate to resource view |
| `Ctrl-A` | Show all available resource aliases |
| `/` | Filter (regex supported) |
| `/-f <word>` | Fuzzy filter |
| `Esc` | Back / clear filter |
| `l` | View logs |
| `s` | Shell into container |
| `d` | Describe resource |
| `e` | Edit resource YAML |
| `Ctrl-D` | Delete resource |
| `Ctrl-K` | Force kill (no confirmation) |
| `Shift-F` | Port-forward |
| `y` | View YAML |
| `r` | Restart deployment/daemonset |
| `Ctrl-R` | Refresh view |
| `:ctx` | Switch cluster context |
| `:ns` | Switch namespace |
