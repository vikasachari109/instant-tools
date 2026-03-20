# LazyGit — macOS Setup

> A simple terminal UI for git commands. Stage files, resolve merge conflicts, view diffs, create branches, and push/pull — all from one interactive interface without typing git commands.

| | |
|---|---|
| **Official Docs** | [github.com/jesseduffield/lazygit](https://github.com/jesseduffield/lazygit) |
| **GitHub** | [github.com/jesseduffield/lazygit](https://github.com/jesseduffield/lazygit) |
| **Version Check** | `lazygit --version` |

---

## ⚡ Quick Install (One Command)

```bash
brew install lazygit
```

---

## 📋 One-Shot Full Setup (Copy & Paste)

```bash
#!/usr/bin/env bash
set -e

echo "📦 Installing LazyGit on macOS..."

if ! command -v brew &>/dev/null; then
  /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
fi

brew install lazygit

# Add shell alias
if ! grep -q 'alias lg=' ~/.zshrc 2>/dev/null; then
  echo '' >> ~/.zshrc
  echo '# LazyGit' >> ~/.zshrc
  echo 'alias lg="lazygit"' >> ~/.zshrc
fi

source ~/.zshrc 2>/dev/null || true

lazygit --version
echo ""
echo "✅ LazyGit installed!"
echo "   Launch in any git repo: lazygit  (or: lg)"
```

---

## 🔧 Configuration

Config file is auto-generated on first launch at:
- macOS: `~/Library/Application Support/lazygit/config.yml`

```yaml
# config.yml — useful customisations
gui:
  theme:
    lightTheme: false
    activeBorderColor:
      - green
      - bold
  showFileTree: true
  nerdFontsVersion: "3"   # enable Nerd Font icons (optional)
git:
  paging:
    colorArg: always
    pager: delta           # use delta for pretty diffs (brew install git-delta)
  commit:
    signOff: false
  autoFetch: true
  autoRefresh: true
keybinding:
  universal:
    quit: q
```

---

## 📚 Keybinding Cheat Sheet

### Navigation

| Key | Action |
|---|---|
| `h / j / k / l` | move left / down / up / right |
| `Tab` | switch to next window/panel |
| `Shift+Tab` | switch to previous panel |
| `[` / `]` | navigate to prev/next tab in panel |
| `q` | quit |

### Files Panel

| Key | Action |
|---|---|
| `Space` | stage / unstage file |
| `a` | stage all / unstage all |
| `c` | commit |
| `C` | commit using editor |
| `A` | amend last commit |
| `i` | ignore file (add to .gitignore) |
| `d` | discard changes in file |
| `e` | open file in editor |
| `Enter` | view diff of selected file |

### Branches Panel

| Key | Action |
|---|---|
| `Space` | checkout branch |
| `n` | create new branch |
| `d` | delete branch |
| `r` | rebase current onto selected |
| `M` | merge selected into current |
| `f` | fast-forward to upstream |
| `g` | reset to this branch |
| `R` | rename branch |

### Commits Panel

| Key | Action |
|---|---|
| `Space` | checkout commit |
| `s` | squash down |
| `r` | reword commit message |
| `d` | drop commit |
| `f` | fixup into the commit above |
| `p` | cherry pick |
| `Ctrl+r` | reset to this commit |

### Global

| Key | Action |
|---|---|
| `P` | push |
| `p` | pull |
| `f` | fetch |
| `?` | show all keybindings |
| `+` / `-` | enlarge/shrink panel |
