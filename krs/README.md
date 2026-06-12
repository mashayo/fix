# 🔄 Mashayo Kernel Recovery Strategy (KRS)

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](https://opensource.org/licenses/MIT)
[![Debian](https://img.shields.io/badge/Debian-12+-blue.svg)](https://www.debian.org/)
[![Ubuntu](https://img.shields.io/badge/Ubuntu-22.04+-orange.svg)](https://ubuntu.com/)
[![JOSS](https://img.shields.io/badge/JOSS-Submitted-lightgrey.svg)](https://joss.theoj.org/)

**Zero‑downtime kernel updates** – automatically prevent and recover from failed kernel upgrades.

[**📄 Read the Paper**](https://mashayo.github.io/krs/) | [**🚀 Quick Start**](#quick-install)

---

## 📌 Overview

The **Mashayo Kernel Recovery Strategy (KRS)** is a lightweight, automated framework that eliminates downtime caused by failed Linux kernel updates, particularly when out‑of‑tree kernel modules (e.g., Broadcom `wl`, NVIDIA, VirtualBox) fail to compile against a new kernel version.

### ✨ Key Features

| Feature | Description |
|---------|-------------|
| **Pre‑update validation** | Checks kernel headers and compiler availability **before** allowing any kernel upgrade |
| **Instant rollback** | Reverts to the previous working kernel in **< 10 seconds** |
| **Zero configuration** | Works out of the box on any Debian/Ubuntu system |
| **DKMS integration** | Seamlessly cooperates with Dynamic Kernel Module Support |
| **Detailed logging** | All actions logged to `/var/log/krs.log` for audit |

### 🎯 When to Use KRS

- You've lost wireless after a kernel update (Broadcom, Realtek, etc.)
- Your graphics driver (NVIDIA, AMD) stopped working after an upgrade
- VirtualBox or VMware modules fail to build against a new kernel
- You want **zero downtime** for critical systems during kernel maintenance

---

## 🔧 The Scripts

| Script | Purpose |
|--------|---------|
| `krs-pre-hook` | APT hook that validates headers/compiler **before** an upgrade proceeds |
| `krs-rollback` | Instantly roll back to the previous working kernel |
| `krs-status` | Diagnostic tool – shows current kernel, rollback target, and critical module status |

---

## 📦 Quick Install

```bash
# 1. Clone the repository
git clone https://github.com/mashayo/fix.git
cd fix/krs

# 2. Make scripts executable
chmod +x krs-pre-hook krs-rollback krs-status

# 3. Install system‑wide
sudo cp krs-pre-hook /usr/local/bin/
sudo cp krs-rollback /usr/local/bin/
sudo cp krs-status /usr/local/bin/

# 4. Install the APT hook (auto‑runs on every apt update)
echo 'APT::Update::Post-Invoke-Success { "/usr/local/bin/krs-pre-hook || true"; };' | sudo tee /etc/apt/apt.conf.d/99-krs-pre-hook

# 5. Ensure your wireless module loads at boot (Broadcom example)
echo "wl" | sudo tee -a /etc/modules
sudo update-initramfs -u
