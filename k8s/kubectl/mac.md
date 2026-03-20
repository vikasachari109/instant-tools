# kubectl — macOS Setup

> The official Kubernetes command-line tool. It allows you to run commands against Kubernetes clusters — deploy apps, inspect resources, view logs, and more.

| | |
|---|---|
| **Official Docs** | [kubernetes.io/docs/tasks/tools/install-kubectl-macos](https://kubernetes.io/docs/tasks/tools/install-kubectl-macos/) |
| **Reference** | [kubectl cheatsheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/) |
| **Version Check** | `kubectl version --client` |

---

## ⚡ Quick Install (One Command)

```bash
brew install kubectl
```

> **Prerequisite:** [Homebrew](https://brew.sh/) — install with:
> ```bash
> /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
> ```

---

## 📋 One-Shot Full Setup (Copy & Paste)

Copy the entire block below, paste it into your terminal, and press Enter.

```bash
#!/usr/bin/env bash
set -e

echo "📦 Setting up kubectl on macOS..."

# ── 1. Install Homebrew if not present ─────────────────────────────────────
if ! command -v brew &>/dev/null; then
  echo "  → Installing Homebrew..."
  /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
fi

# ── 2. Install kubectl ──────────────────────────────────────────────────────
brew install kubectl

# ── 3. Enable zsh shell completion ─────────────────────────────────────────
ZSHRC="$HOME/.zshrc"

if ! grep -q 'kubectl completion zsh' "$ZSHRC" 2>/dev/null; then
  cat >> "$ZSHRC" << 'EOF'

# ── kubectl ─────────────────────────────────────────────────────────────────
source <(kubectl completion zsh)
alias k='kubectl'
complete -F __start_kubectl k
EOF
  echo "  → Shell completion + alias added to ~/.zshrc"
fi

# ── 4. Verify installation ──────────────────────────────────────────────────
kubectl version --client

echo ""
echo "✅ kubectl is ready!"
echo "   Restart your terminal (or run: source ~/.zshrc)"
echo "   Tip: use 'k' as a short alias for 'kubectl'"
```

---

## 🔧 Post-Install Configuration

### Set default editor for `kubectl edit`

```bash
echo 'export KUBE_EDITOR="vim"' >> ~/.zshrc   # or nano / code
```

### Merge multiple kubeconfig files

```bash
# Add to ~/.zshrc to auto-merge all kubeconfig files in ~/.kube/
export KUBECONFIG=$(find ~/.kube -name "*.yaml" -o -name "config" | tr '\n' ':')
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
