# bat — Linux Setup

> A `cat` clone with syntax highlighting, line numbers, git diff integration, and automatic paging. Drop-in replacement for `cat`. Works great as a preview tool with fzf.

| | |
|---|---|
| **GitHub** | [github.com/sharkdp/bat](https://github.com/sharkdp/bat) |
| **Version Check** | `bat --version` |

> **Note on Ubuntu/Debian:** The apt package is called `bat` but the binary may be named `batcat` to avoid a conflict. The setup script handles this automatically.

---

## ⚡ Quick Install (One Command)

**Ubuntu/Debian:**
```bash
sudo apt install bat
```

**Fedora:**
```bash
sudo dnf install bat
```

---

## 📋 One-Shot Full Setup (Copy & Paste)

```bash
#!/usr/bin/env bash
set -e

echo "📦 Setting up bat on Linux..."

# ── 1. Install ──────────────────────────────────────────────────────────────
if command -v apt &>/dev/null; then
  sudo apt-get install -y bat
  # On Ubuntu/Debian the binary might be 'batcat' — create symlink
  if command -v batcat &>/dev/null && ! command -v bat &>/dev/null; then
    mkdir -p "$HOME/.local/bin"
    ln -sf "$(which batcat)" "$HOME/.local/bin/bat"
    echo "  → Created bat → batcat symlink in ~/.local/bin/"
    # Make sure ~/.local/bin is in PATH
    export PATH="$HOME/.local/bin:$PATH"
  fi

elif command -v dnf &>/dev/null; then
  sudo dnf install -y bat

elif command -v pacman &>/dev/null; then
  sudo pacman -S --noconfirm bat

elif command -v brew &>/dev/null; then
  brew install bat

else
  echo "  → Downloading from GitHub releases..."
  ARCH=$(uname -m)
  case "$ARCH" in x86_64) ARCH="x86_64" ;; aarch64) ARCH="aarch64" ;; esac
  LATEST=$(curl -s https://api.github.com/repos/sharkdp/bat/releases/latest \
    | grep '"tag_name"' | cut -d'"' -f4)
  curl -sL "https://github.com/sharkdp/bat/releases/download/${LATEST}/bat-${LATEST}-${ARCH}-unknown-linux-gnu.tar.gz" \
    | tar -xz --strip-components=1 --wildcards '*/bat'
  sudo mv bat /usr/local/bin/bat
fi

# ── 2. Configure ─────────────────────────────────────────────────────────────
BASHRC="$HOME/.bashrc"
if ! grep -q 'BAT_THEME' "$BASHRC" 2>/dev/null; then
  cat >> "$BASHRC" << 'EOF'

# ── bat ──────────────────────────────────────────────────────────────────────
# Ensure ~/.local/bin is in PATH (for batcat → bat symlink on Ubuntu)
export PATH="$HOME/.local/bin:$PATH"

export BAT_THEME="OneHalfDark"
export BAT_STYLE="numbers,changes,header"
export MANPAGER="sh -c 'col -bx | bat -l man -p'"
EOF
fi

if [ -f "$HOME/.zshrc" ] && ! grep -q 'BAT_THEME' "$HOME/.zshrc" 2>/dev/null; then
  echo 'export PATH="$HOME/.local/bin:$PATH"' >> "$HOME/.zshrc"
  echo 'export BAT_THEME="OneHalfDark"' >> "$HOME/.zshrc"
  echo 'export MANPAGER="sh -c '"'"'col -bx | bat -l man -p'"'"'"' >> "$HOME/.zshrc"
fi

# ── 3. Verify ─────────────────────────────────────────────────────────────────
bat --version
echo ""
echo "✅ bat is ready!"
echo "   Usage: bat <file>     → syntax highlighted file view"
```

---

## 📚 Usage Examples

```bash
bat file.yaml                     # view with syntax highlighting
bat file1.yaml file2.yaml         # view multiple files
bat -n file.py                    # show line numbers only
bat -p file.py                    # plain mode (no decorations)
bat --paging=never file.log       # no paging (like cat)
bat -l json <(kubectl get pod -o json)  # highlight piped content

# As fzf preview
fzf --preview 'bat --color=always --line-range :100 {}'

# Show all available themes
bat --list-themes | fzf --preview='bat --theme={} --color=always /etc/hostname'
```
