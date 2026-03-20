# Ranger — Linux Setup

> A powerful terminal-based file manager with vim-style keybindings. Browse directories, preview files, open files with the correct application — all from the terminal.

| | |
|---|---|
| **Official Site** | [ranger.github.io](https://ranger.github.io/) |
| **GitHub** | [github.com/ranger/ranger](https://github.com/ranger/ranger) |
| **Version Check** | `ranger --version` |

---

## ⚡ Quick Install (One Command)

```bash
# Ubuntu/Debian:
sudo apt install -y ranger

# Fedora:
sudo dnf install -y ranger
```

---

## 📋 One-Shot Full Setup (Copy & Paste)

```bash
#!/usr/bin/env bash
set -e

echo "📦 Installing Ranger on Linux..."

if command -v apt &>/dev/null; then
  sudo apt update && sudo apt install -y ranger highlight w3m
elif command -v dnf &>/dev/null; then
  sudo dnf install -y ranger highlight w3m
elif command -v pacman &>/dev/null; then
  sudo pacman -Sy --noconfirm ranger w3m highlight
elif command -v pip3 &>/dev/null; then
  pip3 install ranger-fm
else
  echo "❌ No supported package manager found. Install pip3 and retry."
  exit 1
fi

# Generate default config
ranger --copy-config=all 2>/dev/null || true
echo "✅ Default config generated in ~/.config/ranger/"

# Write rc.conf
mkdir -p ~/.config/ranger
cat > ~/.config/ranger/rc.conf << 'EOF'
set show_hidden true
set preview_files true
set preview_directories true
set unicode_ellipsis true
set draw_borders both
set preview_images true
set preview_images_method w3m
set display_size_in_main_column true
set save_console_history true
set column_ratios 1,3,4
EOF

# Shell integration: cd into directory after ranger exits
for SHELL_RC in ~/.bashrc ~/.zshrc; do
  if [[ -f "$SHELL_RC" ]] && ! grep -q 'ranger_cd' "$SHELL_RC"; then
    cat >> "$SHELL_RC" << 'EOF'

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
  fi
done

source ~/.bashrc 2>/dev/null || true

ranger --version
echo ""
echo "✅ Ranger installed!"
echo "   Launch: ranger  (or just: r)"
echo "   Config: ~/.config/ranger/"
```

---

## 🔧 Configuration

| File | Purpose |
|---|---|
| `~/.config/ranger/rc.conf` | Main settings and keybindings |
| `~/.config/ranger/rifle.conf` | File opener rules |
| `~/.config/ranger/commands.py` | Custom commands |
| `~/.config/ranger/scope.sh` | Preview handling script |

---

## 📚 Keybinding Cheat Sheet

### Navigation

| Key | Action |
|---|---|
| `h / j / k / l` | left / down / up / right (vim-style) |
| `gg` | go to top |
| `G` | go to bottom |
| `~` or `gh` | go to home directory |
| `/` | search |
| `n / N` | next / previous result |

### File Operations

| Key | Action |
|---|---|
| `yy` | copy file |
| `dd` | cut file |
| `pp` | paste |
| `dD` | delete (permanent!) |
| `cw` | rename file |
| `Space` | select/deselect |
| `v` | invert selection |

### View & Toggle

| Key | Action |
|---|---|
| `zh` or `.` | toggle hidden files |
| `zp` | toggle preview |
| `i` | inspect/preview file |
| `!` | run a shell command |
| `S` | open shell in current directory |
| `q` | quit ranger |

### Tabs

| Key | Action |
|---|---|
| `Ctrl+n` | new tab |
| `Tab` | next tab |
| `Shift+Tab` | previous tab |
| `gc` | close current tab |
