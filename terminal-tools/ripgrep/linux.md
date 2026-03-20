# ripgrep (rg) — Linux Setup

> A line-oriented search tool that recursively searches the current directory for a regex pattern. Faster than `grep`, `ag`, and `ack`. Respects `.gitignore` by default.

| | |
|---|---|
| **Official Docs** | [github.com/BurntSushi/ripgrep](https://github.com/BurntSushi/ripgrep) |
| **GitHub** | [github.com/BurntSushi/ripgrep](https://github.com/BurntSushi/ripgrep) |
| **Version Check** | `rg --version` |

---

## ⚡ Quick Install (One Command)

```bash
# Ubuntu/Debian:
sudo apt install -y ripgrep

# Fedora / RHEL:
sudo dnf install -y ripgrep
```

---

## 📋 One-Shot Full Setup (Copy & Paste)

```bash
#!/usr/bin/env bash
set -e

echo "📦 Installing ripgrep on Linux..."

ARCH=$(uname -m)
case $ARCH in
  x86_64)  RG_ARCH="x86_64-unknown-linux-musl" ;;
  aarch64) RG_ARCH="aarch64-unknown-linux-gnu" ;;
  *)       RG_ARCH="x86_64-unknown-linux-musl" ;;
esac

if command -v apt &>/dev/null; then
  sudo apt update && sudo apt install -y ripgrep
elif command -v dnf &>/dev/null; then
  sudo dnf install -y ripgrep
elif command -v pacman &>/dev/null; then
  sudo pacman -Sy --noconfirm ripgrep
elif command -v brew &>/dev/null; then
  brew install ripgrep
else
  # Fallback: download binary from GitHub releases
  echo "Downloading ripgrep binary..."
  RG_VERSION=$(curl -s https://api.github.com/repos/BurntSushi/ripgrep/releases/latest | grep '"tag_name"' | cut -d'"' -f4)
  curl -LO "https://github.com/BurntSushi/ripgrep/releases/download/${RG_VERSION}/ripgrep-${RG_VERSION}-${RG_ARCH}.tar.gz"
  tar xzf "ripgrep-${RG_VERSION}-${RG_ARCH}.tar.gz"
  sudo install -m755 "ripgrep-${RG_VERSION}-${RG_ARCH}/rg" /usr/local/bin/rg
  rm -rf "ripgrep-${RG_VERSION}-${RG_ARCH}"*
fi

# Create ripgrep config file
mkdir -p ~/.config/ripgrep
RIPGREP_CONFIG="$HOME/.config/ripgrep/config"

if [[ ! -f "$RIPGREP_CONFIG" ]]; then
  cat > "$RIPGREP_CONFIG" << 'EOF'
--smart-case
--line-number
--heading
--follow
--glob=!.git/
--glob=!node_modules/
EOF
  echo "✅ Config created at $RIPGREP_CONFIG"
fi

# Write to .bashrc and .zshrc
for SHELL_RC in ~/.bashrc ~/.zshrc; do
  if [[ -f "$SHELL_RC" ]] && ! grep -q 'RIPGREP_CONFIG_PATH' "$SHELL_RC"; then
    echo '' >> "$SHELL_RC"
    echo '# ripgrep' >> "$SHELL_RC"
    echo 'export RIPGREP_CONFIG_PATH="$HOME/.config/ripgrep/config"' >> "$SHELL_RC"
  fi
done

source ~/.bashrc 2>/dev/null || true

rg --version
echo ""
echo "✅ ripgrep installed!"
echo "   Try: rg 'TODO'"
echo "   Try: rg 'pattern' --type py"
```

---

## 🔧 Config File (~/.config/ripgrep/config)

```bash
--smart-case
--line-number
--heading
--follow
--max-filesize=10M
--glob=!.git/
--glob=!node_modules/
--glob=!target/
--glob=!dist/
```

---

## 📚 Cheat Sheet

```bash
rg 'pattern'                        # search recursively
rg -i 'pattern'                     # case-insensitive
rg -l 'pattern'                     # list matching files only
rg -c 'pattern'                     # count matches per file
rg -A 3 -B 3 'pattern'              # context lines
rg 'pattern' --type py              # Python files only
rg 'pattern' -g '*.yaml'            # glob filter
rg --hidden 'pattern'               # include hidden files
rg -u 'pattern'                     # ignore .gitignore

# Interactive with fzf
rg --line-number '' | fzf
```
