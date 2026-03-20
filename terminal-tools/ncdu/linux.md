# ncdu (NCurses Disk Usage) — Linux Setup

> An interactive disk usage analyzer for the terminal. Visualize which directories are consuming the most disk space, and delete unwanted files — all from a fast ncurses UI.

| | |
|---|---|
| **Official Site** | [dev.yorhel.nl/ncdu](https://dev.yorhel.nl/ncdu) |
| **Version Check** | `ncdu --version` |

---

## ⚡ Quick Install (One Command)

**Ubuntu/Debian:**
```bash
sudo apt install ncdu
```

**Fedora/RHEL:**
```bash
sudo dnf install ncdu
```

---

## 📋 One-Shot Full Setup (Copy & Paste)

```bash
#!/usr/bin/env bash
set -e

echo "📦 Installing ncdu on Linux..."

if command -v apt &>/dev/null; then
  sudo apt-get install -y ncdu
elif command -v dnf &>/dev/null; then
  sudo dnf install -y ncdu
elif command -v pacman &>/dev/null; then
  sudo pacman -S --noconfirm ncdu
elif command -v brew &>/dev/null; then
  brew install ncdu
else
  echo "  → No supported package manager found. Install manually from:"
  echo "     https://dev.yorhel.nl/ncdu"
  exit 1
fi

ncdu --version
echo ""
echo "✅ ncdu is ready!"
echo "   Run: ncdu /         → analyze entire disk"
echo "   Run: ncdu ~         → analyze home directory"
```

---

## 📚 Usage

```bash
ncdu                    # analyze current directory
ncdu ~                  # analyze home directory
ncdu /                  # analyze entire filesystem
ncdu --exclude .git .   # exclude patterns
ncdu -x /               # exclude mounted filesystems (local only)

# Remote server: scan over SSH and browse locally
ssh user@server ncdu -o- / | ./ncdu -f-

# Export scan result
ncdu -o /tmp/scan.json  # save scan
ncdu -f /tmp/scan.json  # load and browse scan
```

### Keyboard controls

| Key | Action |
|---|---|
| `↑↓` | Navigate |
| `Enter` | Open directory |
| `d` | Delete selected file/directory |
| `q` | Quit |
| `?` | Help |
| `n` | Sort by name |
| `s` | Sort by size (default) |
| `i` | File info |
