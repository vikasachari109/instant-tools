# vCluster — macOS Setup

> Create fully functional virtual Kubernetes clusters inside a real cluster's namespace. Perfect for multi-tenancy, dev environments, CI/CD isolation, and testing without full cluster overhead.

| | |
|---|---|
| **Official Docs** | [vcluster.com/docs](https://www.vcluster.com/docs) |
| **GitHub** | [github.com/loft-sh/vcluster](https://github.com/loft-sh/vcluster) |
| **Version Check** | `vcluster version` |

---

## ⚡ Quick Install (One Command)

```bash
brew install loft-sh/tap/vcluster
```

---

## 📋 One-Shot Full Setup (Copy & Paste)

```bash
#!/usr/bin/env bash
set -e

echo "📦 Setting up vCluster CLI on macOS..."

# ── 1. Install Homebrew if not present ─────────────────────────────────────
if ! command -v brew &>/dev/null; then
  /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
fi

# ── 2. Install prerequisites: kubectl + helm ────────────────────────────────
command -v kubectl &>/dev/null || brew install kubectl
command -v helm    &>/dev/null || brew install helm

# ── 3. Install vCluster CLI ──────────────────────────────────────────────────
brew install loft-sh/tap/vcluster

# ── 4. Enable zsh completion ─────────────────────────────────────────────────
ZSHRC="$HOME/.zshrc"
if ! grep -q 'vcluster completion' "$ZSHRC" 2>/dev/null; then
  echo '' >> "$ZSHRC"
  echo '# ── vCluster ─────────────────────────────────────────────────────────────────' >> "$ZSHRC"
  echo 'source <(vcluster completion zsh)' >> "$ZSHRC"
fi

# ── 5. Verify ─────────────────────────────────────────────────────────────────
vcluster version

echo ""
echo "✅ vCluster CLI is ready!"
echo ""
echo "Next steps:"
echo "  1. Make sure you're connected to a host cluster: kubectl get nodes"
echo "  2. Create your first vCluster:"
echo "     vcluster create dev --namespace vcluster-dev"
echo "  3. Connect to it:"
echo "     vcluster connect dev --namespace vcluster-dev"
```

---

## 🔧 Usage: Create & Connect to vClusters

### Create a vCluster

```bash
# Create in a new namespace (recommended)
vcluster create <name> --namespace <namespace>

# Example: create a dev vCluster
vcluster create dev --namespace vcluster-dev

# Create with specific Kubernetes version (k3s backend)
vcluster create dev --namespace vcluster-dev \
  --helm-set "vcluster.image=rancher/k3s:v1.29.0-k3s1"

# Create using k8s (full Kubernetes) backend
vcluster create dev --namespace vcluster-dev \
  --distro k8s

# Create and connect immediately (opens new kubeconfig context)
vcluster create dev --namespace vcluster-dev --connect
```

### Connect to a vCluster

```bash
# Connect (opens new terminal with vCluster context active)
vcluster connect <name> --namespace <namespace>

# Connect and run a command inside the vCluster
vcluster connect dev --namespace vcluster-dev -- kubectl get pods
```

### List & Manage vClusters

```bash
vcluster list                               # list all vClusters
vcluster list --namespace <namespace>       # in a specific namespace
vcluster pause <name>    -n <namespace>     # suspend (save resources)
vcluster resume <name>   -n <namespace>     # resume
vcluster delete <name>   -n <namespace>     # delete vCluster
```

---

## 📚 vCluster Config (vcluster.yaml)

```yaml
# vcluster.yaml — customization example
controlPlane:
  distro:
    k3s:
      enabled: true
  statefulSet:
    resources:
      requests:
        memory: 512Mi
        cpu: 100m
      limits:
        memory: 1Gi
        cpu: 500m

sync:
  toHost:
    ingresses:
      enabled: true    # sync ingresses to host cluster
  fromHost:
    nodes:
      enabled: true    # sync nodes info from host
```

```bash
# Apply custom vcluster.yaml
vcluster create dev --namespace vcluster-dev -f vcluster.yaml
```
