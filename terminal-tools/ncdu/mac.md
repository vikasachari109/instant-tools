# ncdu (NCurses Disk Usage) — macOS Setup

> An interactive disk usage analyzer for the terminal. Visualize which directories are consuming the most disk space, and delete unwanted files — all from a fast ncurses UI.

| | |
|---|---|
| **Official Site** | [dev.yorhel.nl/ncdu](https://dev.yorhel.nl/ncdu) |
| **GitHub (ncdu2)** | [code.blicky.net/yorhel/ncdu](https://code.blicky.net/yorhel/ncdu) |
| **Version Check** | `ncdu --version` |

---

## ⚡ Quick Install (One Command)

```bash
brew install ncdu
```

---

## 📋 One-Shot Full Setup (Copy & Paste)

```bash
#!/usr/bin/env bash
set -e

echo "📦 Installing ncdu on macOS..."

if ! command -v brew &>/dev/null; then
  /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
fi

brew install ncdu

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
ncdu -x /               # exclude mounted filesystems (same device only)

# Export scan result and reload later (useful for remote servers)
ncdu -o /tmp/scan.json  # save scan
ncdu -f /tmp/scan.json  # load and browse scan
```

### Keyboard controls inside ncdu

| Key | Action |
|---|---|
| `↑↓` | Navigate |
| `Enter` | Open directory |
| `d` | Delete selected file/directory |
| `r` | Recalculate current directory |
| `q` | Quit |
| `?` | Help |
| `n` | Sort by name |
| `s` | Sort by size (default) |
| `C` | Sort by item count |
| `i` | File info |
| `e` | Toggle exclude hidden files |
