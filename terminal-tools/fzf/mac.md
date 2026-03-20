# fzf (Fuzzy Finder) — macOS Setup

> A blazing-fast, interactive command-line fuzzy finder. Works with any list — files, git commits, command history, processes, Kubernetes resources. Used standalone or as a plugin by kubectx, kubectl, vim, etc.

| | |
|---|---|
| **GitHub** | [github.com/junegunn/fzf](https://github.com/junegunn/fzf) |
| **Version Check** | `fzf --version` |

---

## ⚡ Quick Install (One Command)

```bash
brew install fzf && $(brew --prefix)/opt/fzf/install --all
```

---

## 📋 One-Shot Full Setup (Copy & Paste)

```bash
#!/usr/bin/env bash
set -e

echo "📦 Setting up fzf on macOS..."

# ── 1. Install ──────────────────────────────────────────────────────────────
if ! command -v brew &>/dev/null; then
  /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
fi

brew install fzf

# ── 2. Install keybindings + shell completion ─────────────────────────────
$(brew --prefix)/opt/fzf/install --key-bindings --completion --no-update-rc

# ── 3. Add fzf config to ~/.zshrc ────────────────────────────────────────────
ZSHRC="$HOME/.zshrc"
if ! grep -q 'fzf' "$ZSHRC" 2>/dev/null; then
  cat >> "$ZSHRC" << 'EOF'

# ── fzf ──────────────────────────────────────────────────────────────────────
[ -f ~/.fzf.zsh ] && source ~/.fzf.zsh

# Use fd instead of find (faster, respects .gitignore)
if command -v fd &>/dev/null; then
  export FZF_DEFAULT_COMMAND='fd --type f --hidden --follow --exclude .git'
  export FZF_CTRL_T_COMMAND="$FZF_DEFAULT_COMMAND"
  export FZF_ALT_C_COMMAND='fd --type d --hidden --follow --exclude .git'
fi

# Better preview with bat
if command -v bat &>/dev/null; then
  export FZF_CTRL_T_OPTS="--preview 'bat --color=always --line-range :50 {}'"
fi

# Default options: height, borders, info
export FZF_DEFAULT_OPTS='
  --height 40%
  --layout=reverse
  --border
  --inline-info
  --bind ctrl-/:toggle-preview
'
EOF
  echo "  → fzf config added to ~/.zshrc"
fi

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

## 🔧 Powerful fzf Shell Functions

Add these to your `~/.zshrc` for supercharged workflows:

```bash
# ── fzf power functions ──────────────────────────────────────────────────────

# Fuzzy-search and preview files, then open in editor
fopen() {
  local file
  file=$(fzf --preview 'bat --color=always --line-range :100 {}' \
    --preview-window '~3')
  [ -n "$file" ] && ${EDITOR:-vim} "$file"
}

# Fuzzy git log viewer
fglog() {
  git log --oneline --graph --color=always \
    | fzf --ansi --no-sort --reverse --tiebreak=index \
          --preview 'echo {} | grep -o "[a-f0-9]\{7,\}" | head -1 | xargs git show --color=always' \
          --bind 'enter:execute(echo {} | grep -o "[a-f0-9]\{7,\}" | head -1 | xargs git show | less -R)+abort'
}

# Fuzzy kill process
fkill() {
  local pid
  pid=$(ps aux | tail -n +2 | fzf --multi | awk '{print $2}')
  [ -n "$pid" ] && echo "$pid" | xargs kill -"${1:-9}"
}

# Fuzzy cd (requires fd)
fcd() {
  local dir
  dir=$(fd --type d --hidden --follow --exclude .git . "${1:-.}" \
    | fzf --preview 'ls {}')
  [ -n "$dir" ] && cd "$dir" || return
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
| `Ctrl+/` | Toggle preview window |
| `Enter` | Confirm selection |
| `Esc` | Cancel |
