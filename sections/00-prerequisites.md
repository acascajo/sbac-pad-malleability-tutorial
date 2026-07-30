---
layout: default
title: "Prerequisites & Environment Setup"
nav_order: 0
---

# Prerequisites & Environment Setup

To follow this tutorial you need a system capable of running Docker containers. Below are the specific requirements for each operating system.

---

## System Requirements (All Platforms)

| Requirement | Minimum | Recommended |
|---|---|---|
| RAM | 8 GB | 16 GB |
| Free disk space | 10 GB | 20 GB |
| CPU cores | 2 | 4+ |
| Internet connection | Required for downloading images | — |

---

## Linux

### Docker Installation

**Option A — Docker Engine (native, recommended):**

```bash
# Ubuntu / Debian
sudo apt update && sudo apt install -y docker.io
sudo systemctl enable --now docker

# Fedora
sudo dnf install -y docker
sudo systemctl enable --now docker
```

**Option B — Docker Desktop for Linux:**

Download from [docs.docker.com/desktop/install/linux](https://docs.docker.com/desktop/install/linux/)


---

## macOS

### Docker Installation

**Docker Desktop for Mac (recommended):**

1. Download from [docs.docker.com/desktop/install/mac](https://docs.docker.com/desktop/install/mac/)
2. Choose the **Apple Silicon** version if you have an M1/M2/M3/M4 Mac, or **Intel** version for Intel Macs
3. Drag the `.dmg` into the Applications folder and launch Docker Desktop
4. Accept the terms and follow the setup wizard


---

## Windows

### Docker Installation

**Docker Desktop for Windows (recommended):**

1. Download from [docs.docker.com/desktop/install/windows](https://docs.docker.com/desktop/install/windows/)
2. Run the installer and ensure **"Use WSL 2 instead of Hyper-V"** is selected
3. After installation, launch Docker Desktop
4. It will prompt you to install the WSL2 kernel if missing — follow the instructions

### WSL2 Setup (if needed manually)

If Docker Desktop does not automatically configure WSL2:

```powershell
# Run in PowerShell as Administrator
wsl --install -d ubuntu
```

Then in Docker Desktop → **Settings** → **Resources** → **WSL Integration**, enable integration with your WSL2 distro.

### Docker Desktop Configuration

- Open Docker Desktop → **Settings** → **Resources** → **Advanced**
- Allocate at least **4 CPUs** and **8 GB of memory**
- Under **Settings** → **General**, enable "Use WSL 2 based engine"


---

## Common Verification Checklist

After Docker is installed, confirm everything works:

```bash
# Check Docker version
docker --version

# Run the test container
docker run --rm hello-world

# Verify architecture (on ARM Macs, check emulation if needed)
docker run --rm --platform linux/amd64 alpine uname -m
```

### Expected Output

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

---

<nav style="display: flex; justify-content: space-between; margin-top: 3rem;">
  <a href="../index">← Home</a>
  <a href="01-introduction-and-flexmpi">Next: Part 1 →</a>
</nav>
