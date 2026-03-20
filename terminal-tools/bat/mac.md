# bat — macOS Setup

> A `cat` clone with syntax highlighting, line numbers, git diff integration, and automatic paging. Drop-in replacement for `cat`. Works great as a preview tool with fzf.

| | |
|---|---|
| **GitHub** | [github.com/sharkdp/bat](https://github.com/sharkdp/bat) |
| **Version Check** | `bat --version` |

---

## ⚡ Quick Install (One Command)

```bash
brew install bat
```

---

## 📋 One-Shot Full Setup (Copy & Paste)

```bash
#!/usr/bin/env bash
set -e

echo "📦 Setting up bat on macOS..."

if ! command -v brew &>/dev/null; then
  /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
fi

brew install bat

# ── Configure bat ────────────────────────────────────────────────────────────
ZSHRC="$HOME/.zshrc"
if ! grep -q 'alias cat.*bat' "$ZSHRC" 2>/dev/null; then
  cat >> "$ZSHRC" << 'EOF'

# ── bat ──────────────────────────────────────────────────────────────────────
export BAT_THEME="OneHalfDark"   # or: Dracula, GitHub, Monokai Extended, TwoDark
export BAT_STYLE="numbers,changes,header"

# Use bat as pager for man pages
export MANPAGER="sh -c 'col -bx | bat -l man -p'"

# Aliases (optional — uncomment if you want cat → bat)
# alias cat='bat --paging=never'     # use bat everywhere (no paging)
# alias catp='bat'                   # bat with paging
EOF
  echo "  → bat config added to ~/.zshrc"
fi

bat --version
echo ""
echo "✅ bat is ready!"
echo "   Usage: bat <file>     → syntax highlighted file view"
echo "   Usage: bat *.yaml     → view multiple files"
echo "   Usage: cat file | bat → pipe output through bat"
```

---

## 🔧 Configuration

```bash
# View all available themes
bat --list-themes

# View all available languages
bat --list-languages

# Config file location
bat --config-file         # shows path: usually ~/.config/bat/config

# Example config (~/.config/bat/config)
# --theme="OneHalfDark"
# --style="numbers,changes,header"
# --italic-text=always
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
bat --diff file.diff              # diff view

# As fzf preview
fzf --preview 'bat --color=always --line-range :100 {}'

# Show man page with syntax highlight
man kubectl | bat -l man --paging=always
```
