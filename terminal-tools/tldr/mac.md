# tldr — macOS Setup

> Simplified, community-driven man pages. Instead of reading pages of documentation, `tldr` gives you practical, common-usage examples for any command — instantly.

| | |
|---|---|
| **Official Site** | [tldr.sh](https://tldr.sh/) |
| **GitHub** | [github.com/tldr-pages/tldr](https://github.com/tldr-pages/tldr) |
| **Version Check** | `tldr --version` |

> **Clients:** There are multiple tldr clients. This installs `tlrc` (the official Rust client) via brew, which is fast and well-maintained.

---

## ⚡ Quick Install (One Command)

```bash
brew install tlrc
```

---

## 📋 One-Shot Full Setup (Copy & Paste)

```bash
#!/usr/bin/env bash
set -e

echo "📦 Installing tldr (tlrc) on macOS..."

if ! command -v brew &>/dev/null; then
  /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
fi

# Install tlrc (official Rust-based tldr client)
brew install tlrc

# Update the page database
tldr --update

tldr --version
echo ""
echo "✅ tldr is ready!"
echo "   Try: tldr tar"
echo "   Try: tldr kubectl"
echo "   Try: tldr docker"
```

---

## 📚 Usage

```bash
tldr tar                  # common tar usage examples
tldr kubectl              # kubectl examples
tldr grep                 # grep examples
tldr --update             # update page database
tldr --list               # list all available pages
tldr --language fr <cmd>  # pages in French (if available)

# Search across all pages
tldr --list | fzf | xargs tldr   # fuzzy search tldr pages with fzf
```
