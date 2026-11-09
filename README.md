# Debian Field Hardware & Sysadmin Lab

A hands-on home lab setup built on a bare-metal Debian Linux machine. I created this project to practice system administration, network troubleshooting, device provisioning, and remote access configuration using command-line tools.

---

## 📑 Table of Contents
* [About This Project](#-about-this-project)
* [Tools & Tech Used](#-tools--tech-used)
* [Repo Structure](#-repo-structure)
* [How It Works & Script Demos](#%EF%B8%8F-how-it-works--script-demos)
  * [1. Device Onboarding Script](#1-device-onboarding-script-device_onboardingsh)
  * [2. Network Diagnostic Triage Script](#2-network-diagnostic-triage-script-network_triagesh)
* [Step-by-Step Setup & Verification Guide](#-step-by-step-setup--verification-guide)
* [What I Learned & Key Takeaways](#-what-i-learned--key-takeaways)

---

## 💡 About This Project

I set up this lab to simulate real-world IT scenarios I’ll face in field technical support and systems roles: specifically handling remote device setup, network triage, and SSH management. 

Instead of relying strictly on desktop GUIs, I wanted to practice doing everything through terminal commands, Bash scripts, and remote utilities.

### What I Set Out to Learn & Build:
* **Remote Access & Key Auth:** Setting up OpenSSH on Debian, hardening access rules, and connecting via PuTTY using Ed25519 key pairs.
* **Firewall Hardening:** Configuring basic UFW rules to drop unsolicited traffic while keeping key management ports accessible.
* **Basic Scripting:** Writing Bash scripts to automate routine setup tasks (checking network interfaces, verifying gateway pings, enabling firewall rules) and run quick network diagnostic checks against remote targets.

---

## 🛠 Tools & Tech Used

* **OS:** Debian Linux (Bare-metal installation)
* **Shell & Scripting:** Bash, standard Linux CLI utilities (`ping`, `traceroute`, `netcat`, `awk`)
* **Remote Admin:** OpenSSH, PuTTY (from Windows)
* **Security & Hardening:** UFW (Uncomplicated Firewall), SSH Public Key Authentication

---

## 📁 Repo Structure

```text
debian-sysadmin-lab/
├── README.md
├── scripts/
│   ├── device_onboarding.sh    # Script to check network link and enforce basic firewall rules
│   └── network_triage.sh       # Simple script to test ping, open ports, and traceroute
└── docs/
    └── screenshots/            # Terminal output screenshots and PuTTY setup configs
```

---

## ⚙️ How It Works & Script Demos

### 1. Device Onboarding Script (`device_onboarding.sh`)
This script automates basic checks whenever a new device is hooked up to the local network:
* Verifies outbound internet reachability (`8.8.8.8`)
* Checks if UFW is installed and active (enables default deny incoming if not)
* Prints core host info (Hostname, Local IP address, Kernel version)

```bash
./scripts/device_onboarding.sh
```

### 2. Network Diagnostic Triage Script (`network_triage.sh`)
I wrote this tool to quickly troubleshoot network connectivity and check common enterprise service ports (SSH, HTTP/HTTPS, VPN ports) when a remote host isn't responding:

```bash
./scripts/network_triage.sh 8.8.8.8
```

#### Sample Output from Terminal:
```text
==================================================
 Starting Diagnostic Triage for: 8.8.8.8
 Timestamp: Mon Nov 9 12:00:00 EST 2026
==================================================

[+] Phase 1: Checking ICMP Reachability...
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=117 time=14.2 ms

[+] Phase 2: Auditing Key Ports (22, 80, 443, 1194)...
  - Port 22 : CLOSED / FILTERED
  - Port 80 : OPEN
  - Port 443 : OPEN
  - Port 1194 : CLOSED / FILTERED

[+] Phase 3: Route Path Trace...
 1  192.168.1.1 (192.168.1.1)  1.120 ms
 2  8.8.8.8 (8.8.8.8)  14.105 ms

==================================================
              Triage Sweep Complete               
==================================================
```

---

## 📸 Step-by-Step Setup & Verification Guide

### Step 1: OpenSSH & Firewall Configuration
1. Installed OpenSSH Server and UFW on Debian:
   ```bash
   sudo apt update && sudo apt install -y openssh-server ufw netcat-openbsd traceroute
   ```
2. Applied strict firewall rules allowing only SSH:
   ```bash
   sudo ufw default deny incoming
   sudo ufw default allow outgoing
   sudo ufw allow 22/tcp
   sudo ufw enable
   ```

### Step 2: Key Authentication with PuTTY
1. Generated an **Ed25519 key pair** using PuTTYgen on Windows.
2. Added the public key to `~/.ssh/authorized_keys` on Debian and set permissions to `600`.
3. Updated `/etc/ssh/sshd_config` to enforce key auth and prohibit direct root logins (`PermitRootLogin no`).

---

## 📝 What I Learned & Key Takeaways

* **Sudoers & User Management:** Had to manually add my local non-root user to the `sudo` group after the initial Debian installation (`usermod -aG sudo`).
* **PuTTY Key Conversion:** Learned how PuTTY uses `.ppk` files for OpenSSH key formats and how to configure public key authentication in `~/.ssh/authorized_keys`.
* **CLI Efficiency:** Building diagnostic scripts saves time over running manually typed `ping`, `nc`, and `traceroute` commands one by one during a support triage event.
