# kubectx + kubens — Linux Setup

> Switch between Kubernetes clusters (`kubectx`) and namespaces (`kubens`) in milliseconds. With fzf integration, you get an interactive fuzzy-search menu. Essential for engineers juggling multiple clusters daily.

| | |
|---|---|
| **GitHub** | [github.com/ahmetb/kubectx](https://github.com/ahmetb/kubectx) |
| **Works with** | kubectl, fzf, kube-ps1 |

---

## ⚡ Quick Install (One Command)

**Debian/Ubuntu:**
```bash
sudo apt install kubectx
```

**Via binary (any distro):**
```bash
curl -sLO https://github.com/ahmetb/kubectx/releases/latest/download/kubectx_linux_x86_64.tar.gz \
  && tar -xzf kubectx_linux_x86_64.tar.gz \
  && sudo mv kubectx /usr/local/bin/ && rm kubectx_linux_x86_64.tar.gz

curl -sLO https://github.com/ahmetb/kubectx/releases/latest/download/kubens_linux_x86_64.tar.gz \
  && tar -xzf kubens_linux_x86_64.tar.gz \
  && sudo mv kubens /usr/local/bin/ && rm kubens_linux_x86_64.tar.gz
```

---

## 📋 One-Shot Full Setup (Copy & Paste)

```bash
#!/usr/bin/env bash
set -e

echo "📦 Setting up kubectx + kubens on Linux..."

# ── 1. Detect architecture ──────────────────────────────────────────────────
ARCH=$(uname -m)
case "$ARCH" in
  x86_64)  KCTX_ARCH="x86_64" ;;
  aarch64) KCTX_ARCH="arm64"  ;;
  *)       echo "Unsupported arch: $ARCH"; exit 1 ;;
esac
echo "  → Detected arch: $KCTX_ARCH"

# ── 2. Prefer apt if available (Debian/Ubuntu) ──────────────────────────────
if command -v apt &>/dev/null && apt-cache show kubectx &>/dev/null 2>&1; then
  echo "  → Using apt..."
  sudo apt-get update -qq
  sudo apt-get install -y kubectx   # installs both kubectx AND kubens

else
  echo "  → Installing from GitHub releases..."
  LATEST=$(curl -s https://api.github.com/repos/ahmetb/kubectx/releases/latest \
    | grep '"tag_name"' | cut -d'"' -f4)

  # kubectx
  curl -sL "https://github.com/ahmetb/kubectx/releases/download/${LATEST}/kubectx_${LATEST}_linux_${KCTX_ARCH}.tar.gz" \
    | tar -xz kubectx
  sudo mv kubectx /usr/local/bin/kubectx

  # kubens
  curl -sL "https://github.com/ahmetb/kubectx/releases/download/${LATEST}/kubens_${LATEST}_linux_${KCTX_ARCH}.tar.gz" \
    | tar -xz kubens
  sudo mv kubens /usr/local/bin/kubens
fi

# ── 3. Install fzf for interactive fuzzy search ──────────────────────────────
if ! command -v fzf &>/dev/null; then
  echo "  → Installing fzf..."
  git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf 2>/dev/null || true
  ~/.fzf/install --key-bindings --completion --no-update-rc
fi

# ── 4. Install kube-ps1 ──────────────────────────────────────────────────────
if [ ! -f "$HOME/.kube-ps1/kube-ps1.sh" ]; then
  echo "  → Installing kube-ps1..."
  git clone --depth 1 https://github.com/jonmosco/kube-ps1.git "$HOME/.kube-ps1"
fi

# ── 5. Configure bash ────────────────────────────────────────────────────────
BASHRC="$HOME/.bashrc"
if ! grep -q 'kube-ps1' "$BASHRC" 2>/dev/null; then
  cat >> "$BASHRC" << 'SHELL_CONFIG'

# ── kubectx / kubens / kube-ps1 ──────────────────────────────────────────────
# Shell completion (bash)
[ -f /usr/share/bash-completion/completions/kubectx ] && \
  source /usr/share/bash-completion/completions/kubectx
[ -f /usr/share/bash-completion/completions/kubens ] && \
  source /usr/share/bash-completion/completions/kubens

# kube-ps1 — show cluster/namespace in prompt
source "$HOME/.kube-ps1/kube-ps1.sh"
# Uncomment to add to your PS1:
# PS1='$(kube_ps1) '$PS1

# Aliases
alias kctx='kubectx'
alias kns='kubens'
SHELL_CONFIG
  echo "  → Shell config added to ~/.bashrc"
fi

# ── 6. Verify ─────────────────────────────────────────────────────────────────
echo "  → kubectx: $(which kubectx)"
echo "  → kubens:  $(which kubens)"
echo ""
echo "✅ kubectx + kubens + fzf + kube-ps1 are ready!"
echo "   Restart your terminal (or run: source ~/.bashrc)"
```

---

## 🔧 GCP Cluster Switching Setup

Add to `~/.bashrc` or `~/.zshrc` for fast GCP/GKE cluster management:

```bash
# ── GCP / GKE Cluster Switching ──────────────────────────────────────────────

gke-switch() {
  local project
  if command -v fzf &>/dev/null; then
    project=$(gcloud projects list --format="value(projectId)" | fzf --prompt="Select GCP project: ")
  else
    gcloud projects list --format="table(projectId,name)"
    read -r -p "Enter project ID: " project
  fi

  [ -z "$project" ] && echo "No project selected." && return 1
  gcloud config set project "$project"
  echo "✓ Switched to project: $project"

  echo "Fetching GKE clusters..."
  gcloud container clusters list --format="value(name,location)" | while read -r name location; do
    gcloud container clusters get-credentials "$name" --zone "$location" --project "$project"
    echo "  ✓ Added cluster: $name ($location)"
  done

  echo ""
  kubectx
}

alias gke='gke-switch'
```

---

## 📚 Usage Cheat Sheet

```bash
# kubectx — cluster switching
kubectx                    # list all contexts (interactive with fzf)
kubectx <name>             # switch to a specific context
kubectx -                  # switch back to previous context
kubectx -d <name>          # delete a context
kubectx new-name=old-name  # rename a context

# kubens — namespace switching
kubens                     # list all namespaces (interactive with fzf)
kubens <name>              # switch to a namespace
kubens -                   # switch back to previous namespace
```
