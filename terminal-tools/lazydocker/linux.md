# LazyDocker — Linux Setup

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
curl https://raw.githubusercontent.com/jesseduffield/lazydocker/master/scripts/install_update_linux.sh | bash
```

---

## 📋 One-Shot Full Setup (Copy & Paste)

```bash
#!/usr/bin/env bash
set -e

echo "📦 Installing LazyDocker on Linux..."

# Install Docker if not present
if ! command -v docker &>/dev/null; then
  echo "📦 Docker not found — installing Docker Engine..."
  if command -v apt &>/dev/null; then
    sudo apt update
    sudo apt install -y ca-certificates curl gnupg
    sudo install -m 0755 -d /etc/apt/keyrings
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
    sudo chmod a+r /etc/apt/keyrings/docker.gpg
    echo \
      "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
      $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
      sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    sudo apt update
    sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
    # Allow running docker without sudo
    sudo usermod -aG docker "$USER"
    echo "✅ Docker installed. Log out and back in for group changes to apply."
  else
    echo "❌ Please install Docker manually: https://docs.docker.com/engine/install/"
    exit 1
  fi
fi

# Install LazyDocker
ARCH=$(uname -m)
case $ARCH in
  x86_64)  LDK_ARCH="x86_64" ;;
  aarch64) LDK_ARCH="arm64" ;;
  *)       LDK_ARCH="x86_64" ;;
esac

if command -v brew &>/dev/null; then
  brew install jesseduffield/lazydocker/lazydocker
else
  curl -fsSL https://raw.githubusercontent.com/jesseduffield/lazydocker/master/scripts/install_update_linux.sh | bash
fi

# Add alias
for SHELL_RC in ~/.bashrc ~/.zshrc; do
  if [[ -f "$SHELL_RC" ]] && ! grep -q 'alias ldk=' "$SHELL_RC"; then
    echo '' >> "$SHELL_RC"
    echo '# LazyDocker' >> "$SHELL_RC"
    echo 'alias ldk="lazydocker"' >> "$SHELL_RC"
  fi
done

source ~/.bashrc 2>/dev/null || true

lazydocker --version
echo ""
echo "✅ LazyDocker installed!"
echo "   Ensure Docker is running, then: lazydocker  (or: ldk)"
```

---

## 🔧 Configuration

Config file location:
- Linux: `~/.config/jesseduffield/lazydocker/config.yml`

```yaml
services:
  showAllContainers: true
logs:
  tail: 200
```

---

## 📚 Keybinding Cheat Sheet

### Navigation

| Key | Action |
|---|---|
| `h / j / k / l` | panels and items |
| `Tab` | next panel |
| `Shift+Tab` | previous panel |
| `q` | quit |

### Containers Panel

| Key | Action |
|---|---|
| `Enter` | view logs |
| `s` | stop |
| `r` | restart |
| `d` | remove |
| `e` | exec shell |
| `L` | full log view |
| `m` | metrics (CPU/mem) |

### Images Panel

| Key | Action |
|---|---|
| `d` | remove image |

### Services (Compose)

| Key | Action |
|---|---|
| `s` | stop |
| `r` | restart |
| `u` | up |
| `d` | down |

### Global

| Key | Action |
|---|---|
| `x` | context menu |
| `?` | keybinding help |
