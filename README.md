# ⚡ Instant Tools

> One-shot setup scripts for DevOps / SRE engineers — install any tool on macOS or Linux with a single copy-paste.

Every tool has two files:
- **`mac.md`** — Quick install + full one-shot setup script for macOS (Homebrew/zsh)
- **`linux.md`** — Quick install + full one-shot setup script for Linux (apt/dnf/binary/bash)

Each script handles dependencies, shell config (`.zshrc` / `.bashrc`), completions, and verifies the install.

---

## 📁 Repository Structure

```
instant-tools/
├── k8s/
│   ├── kubectl/            # Kubernetes CLI
│   ├── helm/               # Package manager for Kubernetes
│   ├── k9s/                # Terminal UI for Kubernetes
│   ├── kind/               # Local Kubernetes clusters in Docker
│   ├── vcluster/           # Virtual Kubernetes clusters
│   ├── kubectx-kubens/     # Switch contexts and namespaces fast
│   └── kubectl-aliases/    # 80+ productive kubectl shortcuts
└── terminal-tools/
    ├── fzf/                # Fuzzy finder for the terminal
    ├── bat/                # cat with syntax highlighting
    ├── ncdu/               # Interactive disk usage viewer
    ├── tldr/               # Simplified community-driven man pages
    ├── ripgrep/            # Fastest code/file search (rg)
    ├── ranger/             # Terminal file manager (vim-style)
    ├── lazygit/            # Terminal UI for git
    ├── lazydocker/         # Terminal UI for Docker
    └── glances/            # Real-time system monitoring
```

---

## 🛠 Kubernetes Tools

| Tool | Description | macOS | Linux |
|---|---|---|---|
| **kubectl** | Official Kubernetes CLI | [mac](k8s/kubectl/mac.md) | [linux](k8s/kubectl/linux.md) |
| **helm** | Kubernetes package manager | [mac](k8s/helm/mac.md) | [linux](k8s/helm/linux.md) |
| **k9s** | Terminal UI for Kubernetes | [mac](k8s/k9s/mac.md) | [linux](k8s/k9s/linux.md) |
| **kind** | Local clusters via Docker | [mac](k8s/kind/mac.md) | [linux](k8s/kind/linux.md) |
| **vcluster** | Virtual Kubernetes clusters | [mac](k8s/vcluster/mac.md) | [linux](k8s/vcluster/linux.md) |
| **kubectx / kubens** | Fast context & namespace switching + GKE/GCP helper | [mac](k8s/kubectx-kubens/mac.md) | [linux](k8s/kubectx-kubens/linux.md) |
| **kubectl-aliases** | 80+ kubectl shortcuts & helper functions | [mac](k8s/kubectl-aliases/mac.md) | [linux](k8s/kubectl-aliases/linux.md) |

---

## 💻 Terminal Tools

| Tool | Description | macOS | Linux |
|---|---|---|---|
| **fzf** | Fuzzy finder for files, history, processes | [mac](terminal-tools/fzf/mac.md) | [linux](terminal-tools/fzf/linux.md) |
| **bat** | `cat` replacement with syntax highlighting and Git info | [mac](terminal-tools/bat/mac.md) | [linux](terminal-tools/bat/linux.md) |
| **ncdu** | Interactive disk usage analyser (ncurses) | [mac](terminal-tools/ncdu/mac.md) | [linux](terminal-tools/ncdu/linux.md) |
| **tldr** | Simplified, practical man pages with examples | [mac](terminal-tools/tldr/mac.md) | [linux](terminal-tools/tldr/linux.md) |
| **ripgrep** | Fastest code search — respects `.gitignore` | [mac](terminal-tools/ripgrep/mac.md) | [linux](terminal-tools/ripgrep/linux.md) |
| **ranger** | Terminal file manager with vim keybindings | [mac](terminal-tools/ranger/mac.md) | [linux](terminal-tools/ranger/linux.md) |
| **lazygit** | Terminal UI for git — stage, branch, push/pull | [mac](terminal-tools/lazygit/mac.md) | [linux](terminal-tools/lazygit/linux.md) |
| **lazydocker** | Terminal UI for Docker and Docker Compose | [mac](terminal-tools/lazydocker/mac.md) | [linux](terminal-tools/lazydocker/linux.md) |
| **glances** | Real-time system monitor (CPU, mem, disk, Docker) | [mac](terminal-tools/glances/mac.md) | [linux](terminal-tools/glances/linux.md) |

---

## 🚀 How to Use

1. Click the link for your tool and OS.
2. Copy the **One-Shot Full Setup** script.
3. Paste it into your terminal and run.
4. Done — tool is installed, configured, and ready.

Each file also has a **⚡ Quick Install** one-liner if you just need the binary fast.

---

## 🤝 Contributing

Contributions are welcome!

- **Add a tool:** Create a new sub-folder under `k8s/` or `terminal-tools/` with `mac.md` and `linux.md`.
- **Improve a script:** Fix bugs, update versions, add edge-case handling.
- **Add a new section:** Docker, cloud CLIs (aws/gcloud/az), observability, etc.

**File format:**
- `## ⚡ Quick Install` — single command
- `## 📋 One-Shot Full Setup` — complete bash script (copy-paste ready)
- `## 🔧 Configuration` — post-install config
- `## 📚 Cheat Sheet` — key commands / keybindings

---

## 📄 License

MIT — free to use, fork, and share.
