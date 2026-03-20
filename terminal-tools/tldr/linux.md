# tldr — Linux Setup

> Simplified, community-driven man pages. Instead of reading pages of documentation, `tldr` gives you practical, common-usage examples for any command — instantly.

| | |
|---|---|
| **Official Site** | [tldr.sh](https://tldr.sh/) |
| **GitHub** | [github.com/tldr-pages/tldr](https://github.com/tldr-pages/tldr) |
| **Version Check** | `tldr --version` |

---

## ⚡ Quick Install (One Command)

```bash
# npm (recommended):
npm install -g tldr

# or pip:
pip3 install tldr
```

---

## 📋 One-Shot Full Setup (Copy & Paste)

```bash
#!/usr/bin/env bash
set -e

echo "📦 Installing tldr on Linux..."

# Install tealdeer (Rust binary) — fastest option
ARCH=$(uname -m)
case $ARCH in
  x86_64)   ARCH="x86_64" ;;
  aarch64)  ARCH="aarch64" ;;
  *)        ARCH="x86_64" ;;
esac

TEALDEER_URL="https://github.com/dbrgn/tealdeer/releases/latest/download/tealdeer-linux-${ARCH}-musl"

if curl --output /dev/null --silent --head --fail "$TEALDEER_URL"; then
  curl -fsSL "$TEALDEER_URL" -o /tmp/tldr
  chmod +x /tmp/tldr
  sudo mv /tmp/tldr /usr/local/bin/tldr
  echo "✅ tealdeer (Rust) installed"
elif command -v npm &>/dev/null; then
  npm install -g tldr
  echo "✅ tldr (npm) installed"
elif command -v pip3 &>/dev/null; then
  pip3 install tldr
  echo "✅ tldr (pip) installed"
elif command -v apt &>/dev/null; then
  sudo apt update && sudo apt install -y tldr
  echo "✅ tldr (apt) installed"
else
  echo "❌ Could not install tldr — install npm or pip3 first"
  exit 1
fi

# Update database
tldr --update 2>/dev/null || tldr -u 2>/dev/null || true

tldr --version
echo ""
echo "✅ tldr is ready!"
echo "   Try: tldr tar"
echo "   Try: tldr kubectl"
echo "   Try: tldr grep"

# Add shell alias to source
for SHELL_RC in ~/.bashrc ~/.zshrc; do
  if [[ -f "$SHELL_RC" ]]; then
    if ! grep -q 'tldr --update' "$SHELL_RC" 2>/dev/null; then
      echo '' >> "$SHELL_RC"
      echo '# tldr' >> "$SHELL_RC"
    fi
  fi
done

source ~/.bashrc 2>/dev/null || true
```

---

## 📚 Usage

```bash
tldr tar                  # common tar usage examples
tldr kubectl              # kubectl examples
tldr grep                 # grep examples
tldr --update             # update page database
tldr --list               # list all available pages

# Fuzzy search with fzf
tldr --list | fzf | xargs tldr
```
