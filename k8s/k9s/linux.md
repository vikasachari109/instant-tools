# k9s — Linux Setup

> A terminal-based UI to interact with your Kubernetes clusters in style. Navigate, inspect, manage resources, view logs, exec into pods — all from a fast keyboard-driven TUI.

| | |
|---|---|
| **Official Docs** | [k9scli.io](https://k9scli.io/) |
| **GitHub** | [github.com/derailed/k9s](https://github.com/derailed/k9s) |
| **Version Check** | `k9s version` |

---

## ⚡ Quick Install (One Command)

**Ubuntu/Debian (x86_64):**
```bash
wget https://github.com/derailed/k9s/releases/latest/download/k9s_linux_amd64.deb \
  && sudo apt install ./k9s_linux_amd64.deb && rm k9s_linux_amd64.deb
```

**Snap:**
```bash
sudo snap install k9s --devmode
```

**Fedora (42+):**
```bash
sudo dnf install k9s
```

---

## 📋 One-Shot Full Setup (Copy & Paste)

```bash
#!/usr/bin/env bash
set -e

echo "📦 Setting up k9s on Linux..."

# ── 1. Detect architecture ──────────────────────────────────────────────────
ARCH=$(uname -m)
case "$ARCH" in
  x86_64)  PKG_ARCH="amd64" ;;
  aarch64) PKG_ARCH="arm64" ;;
  *)       echo "Unsupported arch: $ARCH"; exit 1 ;;
esac
echo "  → Detected arch: $PKG_ARCH"

# ── 2. Install k9s ──────────────────────────────────────────────────────────
if command -v apt &>/dev/null; then
  echo "  → Using apt (Debian/Ubuntu)"
  TMP_DEB=$(mktemp --suffix=.deb)
  wget -qO "$TMP_DEB" "https://github.com/derailed/k9s/releases/latest/download/k9s_linux_${PKG_ARCH}.deb"
  sudo apt install -y "$TMP_DEB"
  rm -f "$TMP_DEB"

elif command -v dnf &>/dev/null; then
  echo "  → Using dnf (Fedora/RHEL)"
  sudo dnf install -y k9s

elif command -v snap &>/dev/null; then
  echo "  → Using snap"
  sudo snap install k9s --devmode

elif command -v brew &>/dev/null; then
  echo "  → Using Homebrew"
  brew install derailed/k9s/k9s

else
  echo "  → Downloading binary directly..."
  curl -fsSL "https://github.com/derailed/k9s/releases/latest/download/k9s_linux_${PKG_ARCH}.tar.gz" \
    | tar -xz k9s
  sudo mv k9s /usr/local/bin/k9s
  sudo chmod +x /usr/local/bin/k9s
fi

# ── 3. Set required environment variables ───────────────────────────────────
BASHRC="$HOME/.bashrc"
if ! grep -q 'KUBE_EDITOR' "$BASHRC" 2>/dev/null; then
  cat >> "$BASHRC" << 'EOF'

# ── k9s ──────────────────────────────────────────────────────────────────────
export KUBE_EDITOR="vim"
export TERM=xterm-256color
EOF
  echo "  → Environment variables added to ~/.bashrc"
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

k9s config lives at `~/.config/k9s/` on Linux.

```bash
# View config location
k9s info
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
