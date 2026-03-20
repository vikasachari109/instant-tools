# kubectl Aliases — Linux Setup

> A comprehensive set of kubectl aliases that dramatically speeds up your daily Kubernetes workflow. Same aliases work on both Linux (bash/zsh) and macOS.

See [mac.md](mac.md) for the same content — aliases work identically on Linux. The only difference is the target shell config file.

---

## ⚡ Quick Install (One Command)

**bash:**
```bash
curl -fsSL https://raw.githubusercontent.com/vikasachari109/instant-tools/main/k8s/kubectl-aliases/aliases.sh \
  -o ~/.kubectl_aliases && echo 'source ~/.kubectl_aliases' >> ~/.bashrc && source ~/.bashrc
```

---

## 📋 One-Shot Full Setup (Copy & Paste)

```bash
#!/usr/bin/env bash

cat > "$HOME/.kubectl_aliases" << 'ALIASES'
#!/usr/bin/env bash
# kubectl aliases — source this file from ~/.bashrc or ~/.zshrc

# ── Core ──────────────────────────────────────────────────────────────────────
alias k='kubectl'
alias kl='kubectl logs'
alias kexec='kubectl exec -it'
alias kpf='kubectl port-forward'
alias kaci='kubectl auth can-i'
alias kat='kubectl attach'
alias kapir='kubectl api-resources'
alias kapiv='kubectl api-versions'

# ── get ───────────────────────────────────────────────────────────────────────
alias kg='kubectl get'
alias kgns='kubectl get ns'
alias kgp='kubectl get pods'
alias kgpa='kubectl get pods -A'
alias kgpw='kubectl get pods -w'
alias kgs='kubectl get secrets'
alias kgd='kubectl get deploy'
alias kgrs='kubectl get rs'
alias kgss='kubectl get sts'
alias kgds='kubectl get ds'
alias kgcm='kubectl get configmap'
alias kgcj='kubectl get cronjob'
alias kgj='kubectl get job'
alias kgsvc='kubectl get svc -o wide'
alias kgn='kubectl get no -o wide'
alias kgr='kubectl get roles'
alias kgrb='kubectl get rolebindings'
alias kgcr='kubectl get clusterroles'
alias kgcrb='kubectl get clusterrolebindings'
alias kgsa='kubectl get sa'
alias kgnp='kubectl get netpol'
alias kging='kubectl get ingress'
alias kgpv='kubectl get pv'
alias kgpvc='kubectl get pvc'
alias kghpa='kubectl get hpa'

# ── describe ──────────────────────────────────────────────────────────────────
alias kd='kubectl describe'
alias kdns='kubectl describe ns'
alias kdp='kubectl describe pod'
alias kds='kubectl describe secrets'
alias kdd='kubectl describe deploy'
alias kdrs='kubectl describe rs'
alias kdss='kubectl describe sts'
alias kdds='kubectl describe ds'
alias kdsvc='kubectl describe svc'
alias kdcm='kubectl describe cm'
alias kdcj='kubectl describe cj'
alias kdj='kubectl describe job'
alias kdsa='kubectl describe sa'
alias kdr='kubectl describe roles'
alias kdrb='kubectl describe rolebindings'
alias kdcr='kubectl describe clusterroles'
alias kdcrb='kubectl describe clusterrolebindings'
alias kdnp='kubectl describe netpol'
alias kding='kubectl describe ingress'

# ── edit ──────────────────────────────────────────────────────────────────────
alias ke='kubectl edit'
alias kens='kubectl edit ns'
alias kes='kubectl edit secrets'
alias ked='kubectl edit deploy'
alias kers='kubectl edit rs'
alias kess='kubectl edit sts'
alias keds='kubectl edit ds'
alias kesvc='kubectl edit svc'
alias kecm='kubectl edit cm'
alias kecj='kubectl edit cj'
alias ker='kubectl edit roles'
alias kecr='kubectl edit clusterroles'
alias kerb='kubectl edit clusterrolebindings'
alias kesa='kubectl edit sa'
alias kenp='kubectl edit netpol'

# ── delete ────────────────────────────────────────────────────────────────────
alias kdel='kubectl delete'
alias kdelns='kubectl delete ns'
alias kdels='kubectl delete secrets'
alias kdelp='kubectl delete po'
alias kdeld='kubectl delete deployment'
alias kdelrs='kubectl delete rs'
alias kdelss='kubectl delete sts'
alias kdelds='kubectl delete ds'
alias kdelsvc='kubectl delete svc'
alias kdelcm='kubectl delete cm'
alias kdelcj='kubectl delete cj'
alias kdelj='kubectl delete job'
alias kdelr='kubectl delete roles'
alias kdelrb='kubectl delete rolebindings'
alias kdelcr='kubectl delete clusterroles'
alias kdelcrb='kubectl delete clusterrolebindings'
alias kdelsa='kubectl delete sa'
alias kdelnp='kubectl delete netpol'
alias kdeling='kubectl delete ingress'

# ── logs ──────────────────────────────────────────────────────────────────────
alias klog='kubectl logs -f'
alias klogp='kubectl logs -f --previous'
alias kloga='kubectl logs -f --all-containers=true'

# ── dry-run / mock ────────────────────────────────────────────────────────────
alias kmock='kubectl create --dry-run=client -o yaml'
alias kmockns='kubectl create ns mock --dry-run=client -o yaml'
alias kmockcm='kubectl create cm mock --dry-run=client -o yaml'
alias kmocksa='kubectl create sa mock --dry-run=client -o yaml'

# ── config ────────────────────────────────────────────────────────────────────
alias kcfg='kubectl config'
alias kcfgv='kubectl config view'
alias kcfgns='kubectl config set-context --current --namespace'
alias kcfgctx='kubectl config get-contexts'
alias kcfguse='kubectl config use-context'
alias kcfgcur='kubectl config current-context'

# ── helpers ───────────────────────────────────────────────────────────────────
alias kwatch='watch kubectl get pods -A'

kallns() { kubectl get all -n "${1:-default}"; }
ksh()    { kubectl exec -it "$1" -n "${2:-default}" -- /bin/sh; }
kbash()  { kubectl exec -it "$1" -n "${2:-default}" -- /bin/bash; }

ksecret() {
  kubectl get secret "$1" -n "${2:-default}" -o json \
    | python3 -c "
import json,sys,base64
s=json.load(sys.stdin)
for k,v in s['data'].items():
    print(f'{k}: {base64.b64decode(v).decode()}')
"
}

krestart() { kubectl rollout restart deployment/"$1" -n "${2:-default}"; }
kfwd()     { kubectl port-forward "$1" "$2" -n "${3:-default}"; }

ALIASES

# ── Source from shell config ──────────────────────────────────────────────────
for rc in "$HOME/.bashrc" "$HOME/.zshrc"; do
  if [ -f "$rc" ] && ! grep -q '.kubectl_aliases' "$rc" 2>/dev/null; then
    echo '' >> "$rc"
    echo '# ── kubectl aliases ─────────────────────────────────────────────────────────' >> "$rc"
    echo '[ -f ~/.kubectl_aliases ] && source ~/.kubectl_aliases' >> "$rc"
    echo "  → Aliases sourced in $rc"
  fi
done

source "$HOME/.kubectl_aliases"

echo ""
echo "✅ kubectl aliases installed!"
echo "   hint: type 'alias | grep \"^k\"' to list all k aliases"
```
