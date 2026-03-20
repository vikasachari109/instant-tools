# LazyGit — Linux Setup

> A simple terminal UI for git commands. Stage files, resolve merge conflicts, view diffs, create branches, and push/pull — all from one interactive interface without typing git commands.

| | |
|---|---|
| **Official Docs** | [github.com/jesseduffield/lazygit](https://github.com/jesseduffield/lazygit) |
| **GitHub** | [github.com/jesseduffield/lazygit](https://github.com/jesseduffield/lazygit) |
| **Version Check** | `lazygit --version` |

---

## ⚡ Quick Install (One Command)

```bash
LAZYGIT_VERSION=$(curl -s "https://api.github.com/repos/jesseduffield/lazygit/releases/latest" | grep '"tag_name"' | cut -d'"' -f4 | sed 's/v//') && curl -Lo /tmp/lazygit.tar.gz "https://github.com/jesseduffield/lazygit/releases/download/v${LAZYGIT_VERSION}/lazygit_${LAZYGIT_VERSION}_Linux_x86_64.tar.gz" && tar xf /tmp/lazygit.tar.gz -C /tmp && sudo install /tmp/lazygit /usr/local/bin
```

---

## 📋 One-Shot Full Setup (Copy & Paste)

```bash
#!/usr/bin/env bash
set -e

echo "📦 Installing LazyGit on Linux..."

ARCH=$(uname -m)
case $ARCH in
  x86_64)   LG_ARCH="x86_64" ;;
  aarch64)  LG_ARCH="arm64" ;;
  armv7l)   LG_ARCH="armv6" ;;
  *)        LG_ARCH="x86_64" ;;
esac

if command -v brew &>/dev/null; then
  brew install lazygit
else
  LAZYGIT_VERSION=$(curl -s "https://api.github.com/repos/jesseduffield/lazygit/releases/latest" \
    | grep '"tag_name"' | cut -d'"' -f4 | sed 's/v//')

  echo "Installing lazygit v${LAZYGIT_VERSION} (${LG_ARCH})..."

  curl -Lo /tmp/lazygit.tar.gz \
    "https://github.com/jesseduffield/lazygit/releases/download/v${LAZYGIT_VERSION}/lazygit_${LAZYGIT_VERSION}_Linux_${LG_ARCH}.tar.gz"

  tar xf /tmp/lazygit.tar.gz -C /tmp lazygit
  sudo install -m755 /tmp/lazygit /usr/local/bin/lazygit
  rm /tmp/lazygit.tar.gz /tmp/lazygit 2>/dev/null || true
fi

# Add shell alias
for SHELL_RC in ~/.bashrc ~/.zshrc; do
  if [[ -f "$SHELL_RC" ]] && ! grep -q 'alias lg=' "$SHELL_RC"; then
    echo '' >> "$SHELL_RC"
    echo '# LazyGit' >> "$SHELL_RC"
    echo 'alias lg="lazygit"' >> "$SHELL_RC"
  fi
done

source ~/.bashrc 2>/dev/null || true

lazygit --version
echo ""
echo "✅ LazyGit installed!"
echo "   Launch in any git repo: lazygit  (or: lg)"
```

---

## 🔧 Configuration

Config file location:
- Linux: `~/.config/lazygit/config.yml`

```yaml
# config.yml
gui:
  showFileTree: true
  nerdFontsVersion: "3"
git:
  autoFetch: true
  autoRefresh: true
```

---

## 📚 Keybinding Cheat Sheet

### Navigation

| Key | Action |
|---|---|
| `h / j / k / l` | left / down / up / right |
| `Tab` | next panel |
| `Shift+Tab` | previous panel |
| `q` | quit |

### Files Panel

| Key | Action |
|---|---|
| `Space` | stage / unstage file |
| `a` | stage / unstage all |
| `c` | commit staged changes |
| `C` | commit using editor |
| `A` | amend last commit |
| `i` | add to .gitignore |
| `d` | discard changes |
| `e` | open in editor |

### Branches Panel

| Key | Action |
|---|---|
| `Space` | checkout |
| `n` | new branch |
| `d` | delete branch |
| `r` | rebase current onto selected |
| `M` | merge into current |
| `f` | fast-forward |

### Commits Panel

| Key | Action |
|---|---|
| `s` | squash |
| `r` | reword |
| `d` | drop |
| `f` | fixup |
| `p` | cherry-pick |

### Global

| Key | Action |
|---|---|
| `P` | push |
| `p` | pull |
| `f` | fetch |
| `?` | keybinding help |
