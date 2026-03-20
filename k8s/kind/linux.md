# kind (Kubernetes IN Docker) — Linux Setup

> Run local Kubernetes clusters using Docker containers as nodes. Perfect for development, CI/CD, and testing Kubernetes configurations without a cloud provider.

| | |
|---|---|
| **Official Docs** | [kind.sigs.k8s.io](https://kind.sigs.k8s.io/) |
| **GitHub** | [github.com/kubernetes-sigs/kind](https://github.com/kubernetes-sigs/kind) |
| **Version Check** | `kind version` |

> **Prerequisite:** Docker must be installed and running.
> Install Docker: `curl -fsSL https://get.docker.com | bash`

---

## ⚡ Quick Install (One Command)

```bash
# Auto-detect architecture and install kind
[ $(uname -m) = x86_64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.27.0/kind-linux-amd64
[ $(uname -m) = aarch64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.27.0/kind-linux-arm64
chmod +x ./kind && sudo mv ./kind /usr/local/bin/kind
```

---

## 📋 One-Shot Full Setup (Copy & Paste)

```bash
#!/usr/bin/env bash
set -e

echo "📦 Setting up kind on Linux..."

# ── 1. Install Docker if not present ───────────────────────────────────────
if ! command -v docker &>/dev/null; then
  echo "  → Installing Docker..."
  curl -fsSL https://get.docker.com | bash
  sudo usermod -aG docker "$USER"
  echo "  → Added $USER to docker group (re-login required for group to take effect)"
  newgrp docker
fi

# ── 2. Check Docker is running ──────────────────────────────────────────────
if ! docker info &>/dev/null 2>&1; then
  echo "  → Starting Docker daemon..."
  sudo systemctl start docker
  sudo systemctl enable docker
fi
echo "  → Docker is running ✓"

# ── 3. Install kind ──────────────────────────────────────────────────────────
ARCH=$(uname -m)
case "$ARCH" in
  x86_64)  KIND_ARCH="amd64" ;;
  aarch64) KIND_ARCH="arm64" ;;
  *)       echo "Unsupported arch: $ARCH"; exit 1 ;;
esac

KIND_VERSION=$(curl -s https://api.github.com/repos/kubernetes-sigs/kind/releases/latest | grep '"tag_name"' | cut -d'"' -f4)
echo "  → Installing kind $KIND_VERSION ($KIND_ARCH)..."
curl -Lo ./kind "https://kind.sigs.k8s.io/dl/${KIND_VERSION}/kind-linux-${KIND_ARCH}"
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind

# ── 4. Install kubectl if not present ───────────────────────────────────────
if ! command -v kubectl &>/dev/null; then
  echo "  → Installing kubectl..."
  KUBECTL_VERSION=$(curl -L -s https://dl.k8s.io/release/stable.txt)
  curl -LO "https://dl.k8s.io/release/${KUBECTL_VERSION}/bin/linux/${KIND_ARCH}/kubectl"
  sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
  rm kubectl
fi

# ── 5. Enable bash completion ────────────────────────────────────────────────
BASHRC="$HOME/.bashrc"
if ! grep -q 'kind completion' "$BASHRC" 2>/dev/null; then
  echo '' >> "$BASHRC"
  echo '# ── kind ────────────────────────────────────────────────────────────────────' >> "$BASHRC"
  echo 'source <(kind completion bash)' >> "$BASHRC"
fi

# ── 6. Create a default cluster ─────────────────────────────────────────────
echo ""
echo "  → Creating a default kind cluster..."
kind create cluster --name dev --wait 90s

# ── 7. Verify ─────────────────────────────────────────────────────────────────
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
