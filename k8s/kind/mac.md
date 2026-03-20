# kind (Kubernetes IN Docker) — macOS Setup

> Run local Kubernetes clusters using Docker containers as nodes. Perfect for development, CI/CD, and testing Kubernetes configurations without a cloud provider.

| | |
|---|---|
| **Official Docs** | [kind.sigs.k8s.io](https://kind.sigs.k8s.io/) |
| **GitHub** | [github.com/kubernetes-sigs/kind](https://github.com/kubernetes-sigs/kind) |
| **Version Check** | `kind version` |

> **Prerequisite:** Docker Desktop must be installed and running.
> Install: https://www.docker.com/products/docker-desktop/

---

## ⚡ Quick Install (One Command)

```bash
brew install kind
```

---

## 📋 One-Shot Full Setup (Copy & Paste)

```bash
#!/usr/bin/env bash
set -e

echo "📦 Setting up kind on macOS..."

# ── 1. Check Docker is running ──────────────────────────────────────────────
if ! docker info &>/dev/null 2>&1; then
  echo "❌ Docker is not running. Start Docker Desktop first."
  exit 1
fi
echo "  → Docker is running ✓"

# ── 2. Install Homebrew if not present ─────────────────────────────────────
if ! command -v brew &>/dev/null; then
  /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
fi

# ── 3. Install kind + kubectl (if not already installed) ────────────────────
brew install kind
command -v kubectl &>/dev/null || brew install kubectl

# ── 4. Enable zsh completion ─────────────────────────────────────────────────
ZSHRC="$HOME/.zshrc"
if ! grep -q 'kind completion' "$ZSHRC" 2>/dev/null; then
  echo '' >> "$ZSHRC"
  echo '# ── kind ────────────────────────────────────────────────────────────────────' >> "$ZSHRC"
  echo 'source <(kind completion zsh)' >> "$ZSHRC"
fi

# ── 5. Create a default single-node cluster ──────────────────────────────────
echo ""
echo "  → Creating a default kind cluster..."
kind create cluster --name dev --wait 90s

# ── 6. Verify ─────────────────────────────────────────────────────────────────
kind version
kubectl cluster-info --context kind-dev
kubectl get nodes

echo ""
echo "✅ kind cluster 'dev' is ready!"
echo "   To delete: kind delete cluster --name dev"
```

---

## 🔧 Cluster Configurations

### Single-node cluster (default)

```bash
kind create cluster                        # creates 'kind' cluster
kind create cluster --name myapp           # named cluster
kind create cluster --name myapp --wait 60s
```

### Multi-node cluster (save as `kind-multi.yaml`)

```yaml
# kind-multi.yaml — 1 control-plane + 2 workers
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
  - role: worker
  - role: worker
```

```bash
kind create cluster --config kind-multi.yaml --name multi
```

### HA cluster with port mapping (save as `kind-ha.yaml`)

```yaml
# kind-ha.yaml — 3 control-planes + 2 workers + NodePort mapping
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
    extraPortMappings:
      - containerPort: 80
        hostPort: 80
      - containerPort: 443
        hostPort: 443
  - role: control-plane
  - role: control-plane
  - role: worker
  - role: worker
```

---

## 📚 Essential Commands

```bash
# ── Cluster management ────────────────────────────────────────────────────────
kind create cluster --name <name>          # create cluster
kind create cluster --config config.yaml   # create from config
kind get clusters                          # list all clusters
kind delete cluster --name <name>          # delete cluster

# ── Use cluster with kubectl ──────────────────────────────────────────────────
kubectl cluster-info --context kind-<name>
kubectl config use-context kind-<name>

# ── Load local Docker images into kind ───────────────────────────────────────
kind load docker-image my-app:latest --name <cluster>

# ── Export logs for debugging ─────────────────────────────────────────────────
kind export logs --name <cluster> ./logs/
```
