# fzf (Fuzzy Finder) — Linux Setup

> A blazing-fast, interactive command-line fuzzy finder. Works with any list — files, git commits, command history, processes, Kubernetes resources. Used standalone or as a plugin by kubectx, kubectl, vim, etc.

| | |
|---|---|
| **GitHub** | [github.com/junegunn/fzf](https://github.com/junegunn/fzf) |
| **Version Check** | `fzf --version` |

---

## ⚡ Quick Install (One Command)

```bash
git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf && ~/.fzf/install --all
```

---

## 📋 One-Shot Full Setup (Copy & Paste)

```bash
#!/usr/bin/env bash
set -e

echo "📦 Setting up fzf on Linux..."

# ── 1. Install via package manager or git ───────────────────────────────────
if command -v apt &>/dev/null; then
  # Ubuntu 20.10+ has fzf in repos (may be older version)
  sudo apt-get install -y fzf
  # Also enable keybindings for apt-installed fzf
  if [ -f /usr/share/doc/fzf/examples/key-bindings.zsh ]; then
    echo "  → System fzf installed"
  fi
elif command -v dnf &>/dev/null; then
  sudo dnf install -y fzf
elif command -v pacman &>/dev/null; then
  sudo pacman -S --noconfirm fzf
else
  # Fallback: install from git (always latest)
  echo "  → Installing fzf from GitHub..."
  if [ -d "$HOME/.fzf" ]; then
    git -C "$HOME/.fzf" pull
  else
    git clone --depth 1 https://github.com/junegunn/fzf.git "$HOME/.fzf"
  fi
  "$HOME/.fzf/install" --key-bindings --completion --no-update-rc
fi

# ── 2. Ensure keybindings are loaded in shell configs ────────────────────────
BASHRC="$HOME/.bashrc"
if ! grep -q 'fzf' "$BASHRC" 2>/dev/null; then
  cat >> "$BASHRC" << 'EOF'

# ── fzf ──────────────────────────────────────────────────────────────────────
# Source fzf keybindings (varies by install method)
[ -f ~/.fzf.bash ] && source ~/.fzf.bash
[ -f /usr/share/fzf/key-bindings.bash ] && source /usr/share/fzf/key-bindings.bash
[ -f /usr/share/fzf/completion.bash ] && source /usr/share/fzf/completion.bash
[ -f /usr/share/doc/fzf/examples/key-bindings.bash ] && \
  source /usr/share/doc/fzf/examples/key-bindings.bash

# Use fd if available
if command -v fd &>/dev/null; then
  export FZF_DEFAULT_COMMAND='fd --type f --hidden --follow --exclude .git'
  export FZF_CTRL_T_COMMAND="$FZF_DEFAULT_COMMAND"
  export FZF_ALT_C_COMMAND='fd --type d --hidden --follow --exclude .git'
fi

# Better preview with bat
if command -v bat &>/dev/null; then
  export FZF_CTRL_T_OPTS="--preview 'bat --color=always --line-range :50 {}'"
fi

export FZF_DEFAULT_OPTS='
  --height 40%
  --layout=reverse
  --border
  --inline-info
  --bind ctrl-/:toggle-preview
'
EOF
  echo "  → fzf config added to ~/.bashrc"
fi

if [ -f "$HOME/.zshrc" ] && ! grep -q 'fzf' "$HOME/.zshrc" 2>/dev/null; then
  cat >> "$HOME/.zshrc" << 'EOF'
[ -f ~/.fzf.zsh ] && source ~/.fzf.zsh
[ -f /usr/share/fzf/key-bindings.zsh ] && source /usr/share/fzf/key-bindings.zsh
[ -f /usr/share/fzf/completion.zsh ] && source /usr/share/fzf/completion.zsh
export FZF_DEFAULT_OPTS='--height 40% --layout=reverse --border --inline-info'
EOF
fi

# ── 3. Verify ─────────────────────────────────────────────────────────────────
fzf --version
echo ""
echo "✅ fzf is ready!"
echo ""
echo "Key bindings:"
echo "   Ctrl+R  → fuzzy search command history"
echo "   Ctrl+T  → fuzzy search files in current dir"
echo "   Alt+C   → fuzzy cd into subdirectory"
```

---

## 🔧 Useful Shell Functions

```bash
# Fuzzy file open
fopen() {
  local file
  file=$(fzf --preview 'cat {}')
  [ -n "$file" ] && ${EDITOR:-vim} "$file"
}

# Fuzzy kill process
fkill() {
  local pid
  pid=$(ps aux | tail -n +2 | fzf --multi | awk '{print $2}')
  [ -n "$pid" ] && echo "$pid" | xargs kill -"${1:-9}"
}

# Fuzzy git log viewer
fglog() {
  git log --oneline --graph --color=always \
    | fzf --ansi --no-sort --reverse \
          --preview 'echo {} | grep -o "[a-f0-9]\{7,\}" | head -1 | xargs git show --color=always'
}
```

---

## 📚 Key Bindings

| Key | Action |
|---|---|
| `Ctrl+R` | Fuzzy search command history |
| `Ctrl+T` | Fuzzy search files, paste path to command line |
| `Alt+C` | Fuzzy `cd` into a subdirectory |
| `Tab` | Multi-select |
| `Enter` | Confirm selection |
| `Esc` | Cancel |
