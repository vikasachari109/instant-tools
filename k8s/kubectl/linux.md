# kubectl — Linux Setup

> The official Kubernetes command-line tool. It allows you to run commands against Kubernetes clusters — deploy apps, inspect resources, view logs, and more.

| | |
|---|---|
| **Official Docs** | [kubernetes.io/docs/tasks/tools/install-kubectl-linux](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/) |
| **Reference** | [kubectl cheatsheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/) |
| **Version Check** | `kubectl version --client` |

---

## ⚡ Quick Install (One Command)

**Debian / Ubuntu:**
```bash
sudo apt-get update && sudo apt-get install -y kubectl
```

**Direct binary (any distro):**
```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl" \
  && sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl \
  && rm kubectl
```

---

## 📋 One-Shot Full Setup (Copy & Paste)

Copy the entire block below, paste it into your terminal, and press Enter.

```bash
#!/usr/bin/env bash
set -e

echo "📦 Setting up kubectl on Linux..."

# ── 1. Detect architecture ──────────────────────────────────────────────────
ARCH=$(uname -m)
case "$ARCH" in
  x86_64)  ARCH="amd64" ;;
  aarch64) ARCH="arm64" ;;
  armv7l)  ARCH="arm" ;;
  *)       echo "Unsupported architecture: $ARCH"; exit 1 ;;
esac
echo "  → Detected arch: $ARCH"

# ── 2. Download latest stable kubectl ──────────────────────────────────────
KUBECTL_VERSION=$(curl -L -s https://dl.k8s.io/release/stable.txt)
echo "  → kubectl version: $KUBECTL_VERSION"

curl -LO "https://dl.k8s.io/release/${KUBECTL_VERSION}/bin/linux/${ARCH}/kubectl"

# ── 3. Install ──────────────────────────────────────────────────────────────
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
rm kubectl

# ── 4. Enable bash shell completion ─────────────────────────────────────────
BASHRC="$HOME/.bashrc"

if ! grep -q 'kubectl completion bash' "$BASHRC" 2>/dev/null; then
  cat >> "$BASHRC" << 'EOF'

# ── kubectl ─────────────────────────────────────────────────────────────────
source <(kubectl completion bash)
alias k='kubectl'
complete -o default -F __start_kubectl k
EOF
  echo "  → Shell completion + alias added to ~/.bashrc"
fi

# ── (Optional) zsh completion ───────────────────────────────────────────────
if [ -f "$HOME/.zshrc" ]; then
  if ! grep -q 'kubectl completion zsh' "$HOME/.zshrc" 2>/dev/null; then
    cat >> "$HOME/.zshrc" << 'EOF'

# ── kubectl ─────────────────────────────────────────────────────────────────
source <(kubectl completion zsh)
alias k='kubectl'
complete -F __start_kubectl k
EOF
  fi
fi

# ── 5. Verify installation ──────────────────────────────────────────────────
kubectl version --client

echo ""
echo "✅ kubectl is ready!"
echo "   Restart your terminal (or run: source ~/.bashrc)"
echo "   Tip: use 'k' as a short alias for 'kubectl'"
```

---

## 🔧 Post-Install Configuration

### Set default editor for `kubectl edit`

```bash
echo 'export KUBE_EDITOR="vim"' >> ~/.bashrc
```

### Install via native package manager (Debian/Ubuntu)

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg

curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.32/deb/Release.key \
  | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
sudo chmod 644 /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.32/deb/ /' \
  | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt-get update
sudo apt-get install -y kubectl
```

---

## 📚 Essential Commands Cheat Sheet

```bash
# ── Cluster info ─────────────────────────────────────────────────────────────
kubectl cluster-info
kubectl get nodes -o wide
kubectl config view
kubectl config current-context
kubectl config get-contexts
kubectl config use-context <name>
kubectl config set-context --current --namespace=<namespace>

# ── Pods ──────────────────────────────────────────────────────────────────────
kubectl get pods -A                        # all namespaces
kubectl get pods -n <namespace> -o wide
kubectl describe pod <pod> -n <namespace>
kubectl logs <pod> -n <namespace> -f       # follow logs
kubectl logs <pod> -n <namespace> --previous  # crashed container logs
kubectl exec -it <pod> -n <namespace> -- /bin/sh

# ── Deployments ──────────────────────────────────────────────────────────────
kubectl get deployments -A
kubectl rollout status deployment/<name> -n <namespace>
kubectl rollout history deployment/<name> -n <namespace>
kubectl rollout undo deployment/<name> -n <namespace>
kubectl scale deployment <name> --replicas=3 -n <namespace>

# ── Namespaces ────────────────────────────────────────────────────────────────
kubectl get namespaces
kubectl create namespace <name>
kubectl delete namespace <name>

# ── Resource management ───────────────────────────────────────────────────────
kubectl apply -f <file.yaml>
kubectl delete -f <file.yaml>
kubectl get all -n <namespace>
kubectl top pods -n <namespace>
kubectl top nodes

# ── Dry-run & debugging ───────────────────────────────────────────────────────
kubectl apply -f <file.yaml> --dry-run=client
kubectl create deployment nginx --image=nginx --dry-run=client -o yaml
kubectl diff -f <file.yaml>
```
