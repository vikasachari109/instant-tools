# Ranger — macOS Setup

> A powerful terminal-based file manager with vim-style keybindings. Browse directories, preview files, open files with the correct application — all from the terminal.

| | |
|---|---|
| **Official Site** | [ranger.github.io](https://ranger.github.io/) |
| **GitHub** | [github.com/ranger/ranger](https://github.com/ranger/ranger) |
| **Version Check** | `ranger --version` |

---

## ⚡ Quick Install (One Command)

```bash
brew install ranger
```

---

## 📋 One-Shot Full Setup (Copy & Paste)

```bash
#!/usr/bin/env bash
set -e

echo "📦 Installing Ranger on macOS..."

if ! command -v brew &>/dev/null; then
  /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
fi

# Install ranger and dependencies
brew install ranger highlight w3m

# Generate default config files
ranger --copy-config=all 2>/dev/null || true
echo "✅ Default config files generated in ~/.config/ranger/"

# Create a clean rc.conf with useful settings
mkdir -p ~/.config/ranger
cat > ~/.config/ranger/rc.conf << 'EOF'
# Ranger configuration

# Show hidden files by default
set show_hidden true

# Preview files in the third pane
set preview_files true
set preview_directories true

# Use utf-8 characters for borders
set unicode_ellipsis true
set draw_borders both

# Preview images using w3m (if installed)
set preview_images true
set preview_images_method w3m

# Always display the file size
set display_size_in_main_column true

# Keep settings between sessions
set save_console_history true

# Number of columns in the multi-pane view
set column_ratios 1,3,4
EOF

# Add shell integration: 'ranger --choosedir' lets you cd after quitting
if ! grep -q 'ranger' ~/.zshrc 2>/dev/null; then
  cat >> ~/.zshrc << 'EOF'

# Ranger — change directory on exit
ranger_cd() {
  local tempfile="$(mktemp -t tmp.XXXXXXXXXX)"
  ranger --choosedir="$tempfile" "${@:-$(pwd)}"
  if [[ -f "$tempfile" ]] && [[ "$(cat "$tempfile")" != "$(echo -n "$(pwd)")" ]]; then
    cd "$(cat "$tempfile")"
  fi
  rm -f "$tempfile"
}
alias r="ranger_cd"
EOF
  echo "✅ 'r' alias added — ranger will cd into selected directory on exit"
fi

source ~/.zshrc 2>/dev/null || true

ranger --version
echo ""
echo "✅ Ranger installed!"
echo "   Launch: ranger  (or just: r)"
echo "   Config: ~/.config/ranger/"
```

---

## 🔧 Configuration Files

| File | Purpose |
|---|---|
| `~/.config/ranger/rc.conf` | Main settings and keybindings |
| `~/.config/ranger/rifle.conf` | File opener rules |
| `~/.config/ranger/commands.py` | Custom commands |
| `~/.config/ranger/scope.sh` | File preview script |

---

## 📚 Keybinding Cheat Sheet

### Navigation

| Key | Action |
|---|---|
| `h / j / k / l` | left / down / up / right (vim-style) |
| `gg` | go to top of list |
| `G` | go to bottom |
| `H` | navigate backwards (history) |
| `L` | navigate forwards |
| `~` | go to home directory |
| `gh` | go home (`~`) |
| `/` | search in current folder |
| `n / N` | next / previous search result |

### File Operations

| Key | Action |
|---|---|
| `yy` | yank (copy) file/selection |
| `dd` | cut file/selection |
| `pp` | paste |
| `dD` | delete (permanent!) |
| `cw` | rename file |
| `Space` | select/deselect file |
| `v` | invert selection |
| `uv` | unselect all |

### View & Toggle

| Key | Action |
|---|---|
| `zh` or `.` | toggle hidden files |
| `zp` | toggle file preview |
| `zi` | toggle image preview |
| `i` | inspect / preview current file |
| `!` | run shell command |
| `S` | open shell in current directory |
| `q` | quit ranger |

### Tabs

| Key | Action |
|---|---|
| `Ctrl+n` | new tab |
| `Tab` | switch to next tab |
| `Shift+Tab` | switch to previous tab |
| `gc` | close current tab |
