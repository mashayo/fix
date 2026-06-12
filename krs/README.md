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
```

---

## 🚀 Usage

### Check Current Status

```bash
krs-status
```

Example output:

```text
==========================================
   Mashayo Kernel Recovery Strategy (KRS)
==========================================

Current kernel:        6.1.0-25-amd64
Last booted kernel:    6.1.0-25-amd64
Default next boot:     6.1.0-25-amd64

Critical module status:
  Broadcom wl (WiFi)   : loaded
  NVIDIA (graphics)    : not present or not loaded
```

### Roll Back After a Failed Upgrade

If a kernel update ever breaks your system:

```bash
krs-rollback
```

The script will:
1. Detect the previously booted kernel from GRUB
2. Set it as the default boot option
3. Reboot your system into the working kernel

### The Pre‑update Hook in Action

When you run `sudo apt upgrade`, the `krs-pre-hook` script runs after `apt update`. If the candidate kernel lacks matching headers, you'll see:

```text
KRS: Current kernel = 6.1.0-25-amd64
KRS: Candidate kernel = 6.12.57+deb13-amd64
KRS: ERROR - Header package linux-headers-6.12.57+deb13-amd64 not found.
KRS: Aborting kernel upgrade to prevent downtime.
```

The upgrade is aborted – your system remains untouched.

---

## 📂 Folder Structure

```text
fix/
├── krs/
│   ├── krs-pre-hook       # APT hook script
│   ├── krs-rollback       # Rollback script
│   ├── krs-status         # Diagnostic script
│   ├── paper.md           # JOSS paper (Markdown)
│   └── paper.bib          # Bibliography
└── ...
```

---

## 📖 Theoretical Background

KRS is grounded in the Sequencing Value Condition from Temporal Capital Theory, which states:

> *Binding enablers must be stabilised before irreversible commitment; otherwise, a measurable decay interval begins.*

In kernel updates:
- **Binding enabler:** Matching kernel headers + working compiler
- **Irreversible commitment:** Installing the new kernel and rebooting
- **Decay interval:** Downtime (e.g., no wireless)

KRS enforces this condition by validating the enablers before the upgrade is allowed to proceed.

---

## 📚 References & Related Publications

| Work | DOI | Description |
|------|-----|-------------|
| Temporal Capital Theory | [10.5281/zenodo.20031340](https://doi.org/10.5281/zenodo.20031340) | The Sequencing Value Condition as a general economic law |
| The Constraint Gap | [10.5281/zenodo.18071856](https://doi.org/10.5281/zenodo.18071856) | Why adoption ≠ utilisation when constraints remain |
| Combustion | [10.5281/zenodo.17958334](https://doi.org/10.5281/zenodo.17958334) | Market failure and externality internalisation |
| KRS (full paper) | [mashayo.github.io/krs/](https://mashayo.github.io/krs/) | Complete academic write‑up of this strategy |

---

## 🤝 Contributing

Issues, feature requests, and pull requests are welcome. Please open an issue first to discuss major changes.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the MIT License – see the [LICENSE](LICENSE) file in the repository root.

---

## 📫 Contact & Citation

**Edwin R. Mashayo**  
University of Dar es Salaam, Tanzania  
📧 edwin@mashayo.com  
🔗 ORCID: [0009-0003-1765-5978](https://orcid.org/0009-0003-1765-5978)

To cite this work:

```bibtex
@misc{mashayo2026krs,
  title = {The Mashayo Kernel Recovery Strategy (KRS): A Zero‑Downtime Framework for Linux Kernel Updates},
  author = {Edwin R. Mashayo},
  year = {2026},
  howpublished = {\url{https://github.com/mashayo/fix/tree/main/krs}},
  note = {Submitted to the Journal of Open Source Software}
}
```

---

## ⭐ Show Your Support

If you find KRS useful, please consider starring the repository. It helps others discover the project.

⬆️ **Back to top**
