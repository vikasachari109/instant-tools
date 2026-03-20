# LazyDocker — macOS Setup

> A terminal UI for Docker and Docker Compose. Monitor containers, view logs, restart/stop services, inspect images and volumes — all without memorizing Docker commands.

| | |
|---|---|
| **Official Docs** | [github.com/jesseduffield/lazydocker](https://github.com/jesseduffield/lazydocker) |
| **GitHub** | [github.com/jesseduffield/lazydocker](https://github.com/jesseduffield/lazydocker) |
| **Version Check** | `lazydocker --version` |

> **Requirement:** Docker must be running before launching lazydocker.

---

## ⚡ Quick Install (One Command)

```bash
brew install jesseduffield/lazydocker/lazydocker
```

---

## 📋 One-Shot Full Setup (Copy & Paste)

```bash
#!/usr/bin/env bash
set -e

echo "📦 Installing LazyDocker on macOS..."

if ! command -v brew &>/dev/null; then
  /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
fi

# Check Docker is installed
if ! command -v docker &>/dev/null; then
  echo "⚠️  Docker not found. Install Docker Desktop from https://docs.docker.com/desktop/install/mac/"
  echo "   Then re-run this script."
  exit 1
fi

brew install jesseduffield/lazydocker/lazydocker

# Add shell alias
if ! grep -q 'alias ldk=' ~/.zshrc 2>/dev/null; then
  echo '' >> ~/.zshrc
  echo '# LazyDocker' >> ~/.zshrc
  echo 'alias ldk="lazydocker"' >> ~/.zshrc
fi

source ~/.zshrc 2>/dev/null || true

lazydocker --version
echo ""
echo "✅ LazyDocker installed!"
echo "   Start Docker Desktop, then run: lazydocker  (or: ldk)"
```

---

## 🔧 Configuration

Config file location (auto-generated on first launch):
- macOS: `~/Library/Application Support/jesseduffield/lazydocker/config.yml`

```yaml
# config.yml — common tweaks
gui:
  theme:
    activeBorderColor:
      - green
      - bold
services:
  # Show all containers, not just running ones
  showAllContainers: true
logs:
  # Timestamps in log output
  timestamps: false
  # Number of log lines to show
  tail: 200
```

---

## 📚 Keybinding Cheat Sheet

### Navigation

| Key | Action |
|---|---|
| `h / j / k / l` | move between panels and items |
| `Tab` | next panel |
| `Shift+Tab` | previous panel |
| `[` / `]` | scroll left / right in log view |
| `q` | quit |

### Containers Panel

| Key | Action |
|---|---|
| `Enter` | view container details / logs |
| `s` | stop container |
| `r` | restart container |
| `d` | remove container |
| `e` | exec shell in container |
| `L` | view full container logs |
| `m` | view container metrics (CPU, memory) |
| `b` | bulk actions on containers |

### Images Panel

| Key | Action |
|---|---|
| `Enter` | view image layers |
| `d` | remove image |
| `b` | bulk remove images |

### Volumes Panel

| Key | Action |
|---|---|
| `d` | remove volume |

### Services (Docker Compose) Panel

| Key | Action |
|---|---|
| `Enter` | view logs |
| `s` | stop service |
| `r` | restart service |
| `u` | up (start) service |
| `d` | down (remove) service |

### Networks Panel

| Key | Action |
|---|---|
| `d` | remove network |

### Global

| Key | Action |
|---|---|
| `x` | view context menu for selected item |
| `?` | view keybinding help |
| `+` / `-` | resize active panel |
