# Helm — macOS Setup

> The package manager for Kubernetes. Helm helps you manage Kubernetes applications via reusable, versioned charts — install, upgrade, rollback, and delete complex apps with one command.

| | |
|---|---|
| **Official Docs** | [helm.sh/docs](https://helm.sh/docs/) |
| **GitHub** | [github.com/helm/helm](https://github.com/helm/helm) |
| **ArtifactHub (Charts)** | [artifacthub.io](https://artifacthub.io/) |
| **Version Check** | `helm version` |

---

## ⚡ Quick Install (One Command)

```bash
brew install helm
```

---

## 📋 One-Shot Full Setup (Copy & Paste)

```bash
#!/usr/bin/env bash
set -e

echo "📦 Setting up Helm on macOS..."

# ── 1. Install Homebrew if not present ─────────────────────────────────────
if ! command -v brew &>/dev/null; then
  echo "  → Installing Homebrew..."
  /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
fi

# ── 2. Install Helm ──────────────────────────────────────────────────────────
brew install helm

# ── 3. Add essential chart repositories ─────────────────────────────────────
helm repo add stable    https://charts.helm.sh/stable     2>/dev/null || true
helm repo add bitnami   https://charts.bitnami.com/bitnami 2>/dev/null || true
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx 2>/dev/null || true
helm repo add cert-manager  https://charts.jetstack.io     2>/dev/null || true
helm repo update

echo "  → Repositories added and updated"

# ── 4. Enable zsh completion ─────────────────────────────────────────────────
ZSHRC="$HOME/.zshrc"
if ! grep -q 'helm completion' "$ZSHRC" 2>/dev/null; then
  echo '' >> "$ZSHRC"
  echo '# ── Helm ──────────────────────────────────────────────────────────────────' >> "$ZSHRC"
  echo 'source <(helm completion zsh)' >> "$ZSHRC"
  echo "  → Helm completion added to ~/.zshrc"
fi

# ── 5. Verify ─────────────────────────────────────────────────────────────────
helm version
helm repo list

echo ""
echo "✅ Helm is ready!"
echo "   Restart your terminal (or run: source ~/.zshrc)"
```

---

## 🔧 Essential Helm Usage

### Managing Repositories

```bash
helm repo add <name> <url>       # add a chart repo
helm repo update                  # refresh repo index
helm repo list                    # list added repos
helm repo remove <name>           # remove a repo
helm search repo <keyword>        # search for charts
```

### Installing Charts

```bash
# Install a chart
helm install <release-name> <chart>
helm install nginx ingress-nginx/ingress-nginx -n ingress-nginx --create-namespace

# Install with custom values
helm install <release> <chart> --set key=value
helm install <release> <chart> -f values.yaml

# Dry run (preview without applying)
helm install <release> <chart> --dry-run --debug

# Install with a specific version
helm install <release> <chart> --version 1.2.3
```

### Managing Releases

```bash
helm list -A                          # list all releases (all namespaces)
helm status <release> -n <namespace>  # release status
helm history <release>                # revision history
helm upgrade <release> <chart>        # upgrade a release
helm rollback <release> <revision>    # rollback to revision
helm uninstall <release>              # delete a release
```

### Creating Charts

```bash
helm create my-app                    # scaffold a new chart
helm lint my-app                      # validate chart
helm package my-app                   # package chart as .tgz
helm template my-app ./my-app/        # render templates locally
```

---

## 📚 Useful Chart Repos

| Name | URL |
|---|---|
| Bitnami | `https://charts.bitnami.com/bitnami` |
| Ingress-Nginx | `https://kubernetes.github.io/ingress-nginx` |
| Cert-Manager | `https://charts.jetstack.io` |
| Prometheus Community | `https://prometheus-community.github.io/helm-charts` |
| Grafana | `https://grafana.github.io/helm-charts` |
| ArgoCD | `https://argoproj.github.io/argo-helm` |
