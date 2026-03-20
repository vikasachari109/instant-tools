# ripgrep (rg) — macOS Setup

> A line-oriented search tool that recursively searches the current directory for a regex pattern. Faster than `grep`, `ag`, and `ack`. Respects `.gitignore` by default.

| | |
|---|---|
| **Official Docs** | [github.com/BurntSushi/ripgrep](https://github.com/BurntSushi/ripgrep) |
| **GitHub** | [github.com/BurntSushi/ripgrep](https://github.com/BurntSushi/ripgrep) |
| **Version Check** | `rg --version` |

---

## ⚡ Quick Install (One Command)

```bash
brew install ripgrep
```

---

## 📋 One-Shot Full Setup (Copy & Paste)

```bash
#!/usr/bin/env bash
set -e

echo "📦 Installing ripgrep on macOS..."

if ! command -v brew &>/dev/null; then
  /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
fi

brew install ripgrep

# Create ripgrep config file
mkdir -p ~/.config/ripgrep
RIPGREP_CONFIG="$HOME/.config/ripgrep/config"

if [[ ! -f "$RIPGREP_CONFIG" ]]; then
  cat > "$RIPGREP_CONFIG" << 'EOF'
# ripgrep config file
# Smart case search (ignore case if pattern is all lowercase)
--smart-case

# Pretty output with line numbers
--line-number

# Follow symlinks
--follow

# Always print file names for multiple file results
--heading
EOF
  echo "✅ ripgrep config created at $RIPGREP_CONFIG"
fi

# Set RIPGREP_CONFIG_PATH in shell config
if ! grep -q 'RIPGREP_CONFIG_PATH' ~/.zshrc 2>/dev/null; then
  echo '' >> ~/.zshrc
  echo '# ripgrep' >> ~/.zshrc
  echo 'export RIPGREP_CONFIG_PATH="$HOME/.config/ripgrep/config"' >> ~/.zshrc
fi

source ~/.zshrc 2>/dev/null || true

rg --version
echo ""
echo "✅ ripgrep installed!"
echo "   Try: rg 'search_term'"
echo "   Try: rg 'TODO' --type py"
```

---

## 🔧 Config File (~/.config/ripgrep/config)

```bash
# Smart case search
--smart-case

# Show line numbers
--line-number

# Show filenames as headings
--heading

# Max file size to search (skip large binary blobs)
--max-filesize=10M

# Follow symbolic links
--follow

# Exclude common directories
--glob=!.git/
--glob=!node_modules/
--glob=!target/
--glob=!dist/
```

---

## 📚 Cheat Sheet

```bash
# Basic search
rg 'pattern'                        # search in current directory
rg 'pattern' file.txt               # search in specific file
rg -i 'pattern'                     # case-insensitive search
rg -l 'pattern'                     # list files containing matches
rg -c 'pattern'                     # count matches per file

# File type filtering
rg 'pattern' --type py              # Python files only
rg 'pattern' --type js              # JavaScript files only
rg 'pattern' -g '*.yaml'            # glob pattern for .yaml only
rg 'pattern' -g '!*.min.js'         # exclude minified JS

# Context
rg -A 3 'pattern'                  # 3 lines after match
rg -B 3 'pattern'                  # 3 lines before match
rg -C 3 'pattern'                  # 3 lines context (before + after)

# Advanced
rg 'class\s+\w+' --type py          # regex for class definitions
rg -r 'new_name' 'old_name'         # replace (preview only — use sed to mutate)
rg --hidden 'pattern'               # search hidden files
rg -u 'pattern'                     # ignore .gitignore
rg -n 'TODO' --sort path            # sort results by filename

# With fzf for interactive search
rg --line-number '' | fzf
```
