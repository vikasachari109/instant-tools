# kubectx + kubens — macOS Setup

> Switch between Kubernetes clusters (`kubectx`) and namespaces (`kubens`) in milliseconds. With fzf integration, you get an interactive fuzzy-search menu. Essential for engineers juggling multiple clusters daily.

| | |
|---|---|
| **GitHub** | [github.com/ahmetb/kubectx](https://github.com/ahmetb/kubectx) |
| **Works with** | kubectl, fzf, oh-my-zsh / kube-ps1 |

---

## ⚡ Quick Install (One Command)

```bash
brew install kubectx fzf
```

---

## 📋 One-Shot Full Setup (Copy & Paste)

This script installs `kubectx`, `kubens`, `fzf`, `kube-ps1` (shows cluster/namespace in shell prompt), and configures GCP cluster switching.

```bash
#!/usr/bin/env bash
set -e

echo "📦 Setting up kubectx + kubens on macOS..."

# ── 1. Install Homebrew if not present ─────────────────────────────────────
if ! command -v brew &>/dev/null; then
  /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
fi

# ── 2. Install tools ─────────────────────────────────────────────────────────
brew install \
  kubectx \    # kubectx + kubens included
  fzf \        # fuzzy finder (enables interactive menu in kubectx/kubens)
  kube-ps1     # shows (cluster|namespace) in your shell prompt

# ── 3. Configure fzf key bindings ────────────────────────────────────────────
$(brew --prefix)/opt/fzf/install --key-bindings --completion --no-update-rc 2>/dev/null || true

# ── 4. Configure shell (zsh) ──────────────────────────────────────────────────
ZSHRC="$HOME/.zshrc"
if ! grep -q 'kube-ps1' "$ZSHRC" 2>/dev/null; then
  cat >> "$ZSHRC" << 'SHELL_CONFIG'

# ── kubectx / kubens / kube-ps1 ──────────────────────────────────────────────
source "$(brew --prefix)/opt/kube-ps1/share/kube-ps1.sh"

# Show (cluster|namespace) in your prompt — add $(kube_ps1) to your PS1
# Uncomment ONE of the following depending on your prompt style:
# PROMPT='$(kube_ps1) '$PROMPT                     # prepend to existing prompt
# export PS1='[\u@\h \W $(kube_ps1)]\$ '           # simple custom prompt

# Handy aliases
alias kctx='kubectx'     # switch cluster context
alias kns='kubens'       # switch namespace

# kube-ps1 toggle helpers
kubeoff()  { kubeoff; }   # hide kube info in prompt
kubeon()   { kubeon; }    # show kube info in prompt
SHELL_CONFIG
  echo "  → Shell config added to ~/.zshrc"
fi

# ── 5. Verify ─────────────────────────────────────────────────────────────────
kubectx --version 2>/dev/null || kubectx 2>/dev/null && echo "  → kubectx ✓"
kubens  --version 2>/dev/null || kubens  2>/dev/null && echo "  → kubens ✓"

echo ""
echo "✅ kubectx + kubens + fzf + kube-ps1 are ready!"
echo "   Restart your terminal (or run: source ~/.zshrc)"
echo ""
echo "Usage:"
echo "   kubectx            → list contexts (fuzzy select with fzf)"
echo "   kubectx <name>     → switch to context"
echo "   kubectx -          → switch to previous context"
echo "   kubens             → list namespaces (fuzzy select with fzf)"
echo "   kubens <name>      → switch namespace"
```

---

## 🔧 GCP Cluster Switching Setup

If you work with GCP/GKE clusters, add the following to your `~/.zshrc` for quick cluster access:

```bash
# ── GCP / GKE Cluster Switching ──────────────────────────────────────────────

# Quick project switch + fetch all GKE clusters
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

  # Fetch and register all GKE clusters in this project
  echo "Fetching GKE clusters..."
  gcloud container clusters list --format="value(name,location)" | while read -r name location; do
    gcloud container clusters get-credentials "$name" --zone "$location" --project "$project"
    echo "  ✓ Added cluster: $name ($location)"
  done

  echo ""
  echo "Current clusters:"
  kubectx
}

# Quick alias to switch GKE cluster with fuzzy search
alias gke='gke-switch'
```

---

## 📚 Usage Cheat Sheet

```bash
# kubectx — cluster switching
kubectx                    # list all contexts (interactive with fzf)
kubectx <name>             # switch to a specific context
kubectx -                  # switch back to previous context
kubectx -s <name>          # start isolated shell with this context only
kubectx -d <name>          # delete a context
kubectx new-name=old-name  # rename a context

# kubens — namespace switching
kubens                     # list all namespaces (interactive with fzf)
kubens <name>              # switch to a namespace
kubens -                   # switch back to previous namespace

# GKE helpers (requires gcloud)
gcloud container clusters list                           # list all GKE clusters
gcloud container clusters get-credentials <name> \
  --zone <zone> --project <project>                     # add cluster to kubeconfig
```
