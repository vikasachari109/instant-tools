# vCluster — Linux Setup

> Create fully functional virtual Kubernetes clusters inside a real cluster's namespace. Perfect for multi-tenancy, dev environments, CI/CD isolation, and testing without full cluster overhead.

| | |
|---|---|
| **Official Docs** | [vcluster.com/docs](https://www.vcluster.com/docs) |
| **GitHub** | [github.com/loft-sh/vcluster](https://github.com/loft-sh/vcluster) |
| **Version Check** | `vcluster version` |

---

## ⚡ Quick Install (One Command)

```bash
curl -L -o vcluster "https://github.com/loft-sh/vcluster/releases/latest/download/vcluster-linux-amd64" \
  && sudo mv vcluster /usr/local/bin/vcluster \
  && sudo chmod +x /usr/local/bin/vcluster
```

---

## 📋 One-Shot Full Setup (Copy & Paste)

```bash
#!/usr/bin/env bash
set -e

echo "📦 Setting up vCluster CLI on Linux..."

# ── 1. Detect architecture ──────────────────────────────────────────────────
ARCH=$(uname -m)
case "$ARCH" in
  x86_64)  VCLUSTER_ARCH="amd64" ;;
  aarch64) VCLUSTER_ARCH="arm64" ;;
  *)       echo "Unsupported arch: $ARCH"; exit 1 ;;
esac
echo "  → Detected arch: $VCLUSTER_ARCH"

# ── 2. Install prerequisites ─────────────────────────────────────────────────
# kubectl
if ! command -v kubectl &>/dev/null; then
  echo "  → Installing kubectl..."
  KUBECTL_VERSION=$(curl -L -s https://dl.k8s.io/release/stable.txt)
  curl -LO "https://dl.k8s.io/release/${KUBECTL_VERSION}/bin/linux/${VCLUSTER_ARCH}/kubectl"
  sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl && rm kubectl
fi

# helm
if ! command -v helm &>/dev/null; then
  echo "  → Installing helm..."
  curl -fsSL https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
fi

# ── 3. Install vCluster CLI ──────────────────────────────────────────────────
echo "  → Downloading vCluster CLI..."
curl -L -o /tmp/vcluster \
  "https://github.com/loft-sh/vcluster/releases/latest/download/vcluster-linux-${VCLUSTER_ARCH}"

sudo mv /tmp/vcluster /usr/local/bin/vcluster
sudo chmod +x /usr/local/bin/vcluster

# ── 4. Enable bash completion ────────────────────────────────────────────────
BASHRC="$HOME/.bashrc"
if ! grep -q 'vcluster completion' "$BASHRC" 2>/dev/null; then
  echo '' >> "$BASHRC"
  echo '# ── vCluster ─────────────────────────────────────────────────────────────────' >> "$BASHRC"
  echo 'source <(vcluster completion bash)' >> "$BASHRC"
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
