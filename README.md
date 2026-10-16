<div align="center">

# 🛡️ Cybersecurity Lab Setup

### Kali Linux Virtual Lab on a Private NAT Network

**Networkwalks Cybersecurity Internship | Week 1 | Module WK1-PM1**

![Status](https://img.shields.io/badge/status-completed-brightgreen)
![Platform](https://img.shields.io/badge/hypervisor-VMware%20Workstation-blue)
![OS](https://img.shields.io/badge/guest-Kali%20Linux-557C94?logo=kalilinux&logoColor=white)
![Network](https://img.shields.io/badge/network-10.0.0.0%2F24-C00000)
![Use](https://img.shields.io/badge/use-educational-lightgrey)

*Prepared by **Shan Rasool***

</div>

---

## 📑 Table of Contents

1. [Overview](#-overview)
2. [Project Objectives](#-project-objectives)
3. [Lab Environment](#-lab-environment)
4. [Network Design](#-network-design)
5. [Step-by-Step Setup Guide](#-step-by-step-setup-guide)
6. [Verification and Evidence](#-verification-and-evidence)
7. [Results Summary](#-results-summary)
8. [Troubleshooting Guide](#-troubleshooting-guide)
9. [Command Reference](#-command-reference)
10. [Security Best Practices](#-security-best-practices)
11. [Roadmap](#-roadmap)
12. [Repository Structure](#-repository-structure)
13. [Key Learnings](#-key-learnings)
14. [Disclaimer](#-disclaimer)
15. [Author](#-author)

---

## 🔎 Overview

This repository documents **Project Module WK1-PM1 (Lab Setup)** from the Networkwalks Cybersecurity Internship. The aim of the module is to build a personal cybersecurity testing lab on a laptop or PC, with **Kali Linux** as the attacking and analysis machine, running on a private virtual network with full internet access.

A home lab gives a safe, isolated place to practise security skills. Tools can be run, systems can be misconfigured and repaired, and snapshots allow a fast rollback, all without touching real systems or real networks.

📄 The full formatted report is available in [`Report.pdf`](NetworkWalks-Week_01-Cybersecurity-Lab_Setup/blob/main/Shan_Rasool_WK1-PM1_Lab_Setup_Report.pdf).

---

## 🎯 Project Objectives

The module brief asks for the following:

- [x] Build a cybersecurity testing lab on a laptop or PC using a desktop hypervisor
- [x] Set up Kali Linux as the attacking (hacker) machine
- [x] Use a private virtual network in the `10.0.0.0/24` subnet
- [x] Give Kali Linux full internet access and verify it
- [ ] Enable clipboard sharing and file drag-and-drop *(evidence to be added)*
- [ ] Share the host's Downloads folder with the VM *(evidence to be added)*

> **Honest note on the brief:** the original brief names VirtualBox, a NATNetwork, and a manual Kali address of `10.0.0.2/24`. This lab was built on **VMware Workstation**, where `10.0.0.2` is the NAT gateway itself, so Kali receives `10.0.0.128` through DHCP. The `10.0.0.0/24` subnet matches the brief.

---

## 🖥️ Lab Environment

| Component | Details |
|---|---|
| **Hypervisor** | VMware Workstation (Windows host) |
| **Guest OS** | Kali Linux |
| **VM name** | `kali Linux` |
| **Role** | Attacking / analysis machine |
| **Memory** | 2 GB |
| **Processors** | 1 |
| **Disk** | 20 GB (SCSI) |
| **Install media** | Kali Linux ISO attached as virtual CD/DVD (SATA) |
| **Network adapters** | 3: NAT, Custom (VMnet2), Custom (VMnet8 NAT) |

### Recommended host specifications

| Resource | Minimum | Recommended |
|---|---|---|
| RAM | 8 GB | 16 GB |
| Storage | 256 GB SSD | 512 GB SSD |
| CPU | Core i3 (virtualization enabled) | Core i5 or better |

---

## 🌐 Network Design

```text
                    ┌──────────────────────────────┐
                    │      Windows Host (PC)       │
                    │                              │
                    │   ┌──────────────────────┐   │
                    │   │  VMware Workstation  │   │
                    │   │                      │   │
                    │   │  ┌────────────────┐  │   │
                    │   │  │  Kali Linux    │  │   │
                    │   │  │  eth2          │  │   │
                    │   │  │  10.0.0.128/24 │  │   │
                    │   │  └───────┬────────┘  │   │
                    │   │          │           │   │
                    │   │   VMnet8 (NAT)       │   │
                    │   │   Gateway 10.0.0.2   │   │
                    │   └──────────┬───────────┘   │
                    └──────────────┼───────────────┘
                                   │
                              🌍 Internet
```

### Addressing observed in the lab

| Parameter | Value | Source command |
|---|---|---|
| Active interface | `eth2` (state **UP**) | `ip -4 addr show eth2` |
| IPv4 address | `10.0.0.128/24` | `ip -4 addr show eth2` |
| Broadcast | `10.0.0.255` | `ip -4 addr show eth2` |
| Assignment | Dynamic (DHCP) | `ip route` (`proto dhcp`) |
| Default gateway | `10.0.0.2` | `ip route` |
| Local route | `10.0.0.0/24` via `eth2` | `ip route` |
| Virtual network | `VMnet8 (NAT)` | VM settings |

---

## 🧭 Step-by-Step Setup Guide

### Step 1: Install the hypervisor

Install the desktop hypervisor on the Windows host and make sure hardware virtualization (Intel VT-x or AMD-V) is enabled in the BIOS/UEFI.

### Step 2: Create the Kali Linux virtual machine

1. Download the Kali Linux image from the official site: <https://www.kali.org/get-kali/>
2. Create a new VM named `kali Linux`.
3. Assign **2 GB memory**, **1 processor** and a **20 GB disk**.
4. Attach the Kali ISO as the virtual CD/DVD drive.
5. Boot the VM and complete the installation.

### Step 3: Configure networking

1. Open **Virtual Machine Settings → Hardware**.
2. Add or select a network adapter and choose **Custom: Specific virtual network → VMnet8 (NAT)**.
3. Tick **Connect at power on**.
4. Start the VM.

### Step 4: Confirm the address and route

```bash
ip -4 addr show eth2
ip route
```

### Step 5: Test connectivity

```bash
ping -c 4 10.0.0.2      # gateway
ping -c 4 google.com    # DNS + internet
```

### Step 6: Take a snapshot

Create a snapshot of the working configuration (for example `Kali-Base-Configuration`) so you can roll back after any experiment.

---

## ✅ Verification and Evidence

### 1. Interface address

![ip addr](images/01-ip-addr.png)

`eth2` is **UP** with `10.0.0.128/24`. The `dynamic` flag shows the address came from DHCP.

### 2. Routing table

![ip route](images/02-ip-route.png)

The default route goes via `10.0.0.2` (the NAT gateway) and the local network `10.0.0.0/24` is attached to `eth2`.

### 3. Connectivity tests

![ping tests](images/03-ping-tests.png)

| Test | Command | Sent / Received | Loss | Avg RTT |
|---|---|---|---|---|
| Gateway | `ping -c 4 10.0.0.2` | 4 / 4 | 0% | 0.599 ms |
| Internet (DNS + ICMP) | `ping -c 4 google.com` | 4 / 4 | 0% | 64.413 ms |

Pinging a hostname proves **two** things at once: DNS resolution works, and outbound internet access through NAT works.

### 4. Virtual machine settings

![vm settings](images/04-vm-settings.png)

Network Adapter 3 is set to **Custom (VMnet8 NAT)** with **Connect at power on** enabled.

---

## 📊 Results Summary

| # | Requirement / test | Result |
|---|---|---|
| 1 | Kali Linux deployed as attacking machine | ✅ Done |
| 2 | Network in `10.0.0.0/24` | ✅ Done |
| 3 | Gateway reachable | ✅ 0% loss |
| 4 | Full internet access | ✅ 0% loss |
| 5 | DNS resolution | ✅ Pass |
| 6 | Kali address `10.0.0.2/24` | ⚠️ Differs: `10.0.0.128/24` via DHCP |
| 7 | VirtualBox with NATNetwork | ⚠️ Differs: VMware Workstation, VMnet8 |
| 8 | Clipboard and drag-and-drop | ⏳ Evidence to be added |
| 9 | Shared Downloads folder | ⏳ Evidence to be added |

---

## 🧰 Troubleshooting Guide

| Symptom | Likely cause | Fix |
|---|---|---|
| No internet after boot | Duplicate address detection delay, or adapter not on NAT | See the `nmcli` fix below |
| Gateway pings, `google.com` fails | DNS not resolving | Set DNS to `8.8.8.8`, or use the gateway address |
| Address conflict | Two VMs share one IP | Give each VM a unique address and restart |
| Interface has no address | Adapter disconnected at boot | Enable **Connect at power on** |

### Fix for VirtualBox 7 with Kali 2026.1 or newer

```bash
sudo nmcli connection modify "Wired connection 1" ipv4.dad-timeout 0
sudo nmcli connection down "Wired connection 1"
sudo nmcli connection up "Wired connection 1"
```

### General checklist

1. Confirm the virtual network exists and is configured correctly.
2. Confirm the adapter is attached to the right network and connected.
3. Confirm no other VM uses the same IP.
4. Restart the VM, then the hypervisor, then the host if needed.

---

## 📚 Command Reference

| Command | Purpose |
|---|---|
| `ip -4 addr show eth2` | Show IPv4 address, mask and state of an interface |
| `ip route` | Show the default gateway and local routes |
| `ping -c 4 <host>` | Send 4 echo requests and report loss and latency |
| `sudo apt update && sudo apt full-upgrade` | Update Kali packages |
| `nmcli connection show` | List NetworkManager connections |

---

## 🔐 Security Best Practices

- Keep the lab on an **isolated virtual network**; avoid bridged mode unless you need it.
- Take **snapshots** before every experiment.
- Never run offensive tools against systems you do not own or have **written permission** to test.
- Keep Kali and the hypervisor **updated**.
- Do not store real credentials or personal data inside lab machines.
- Limit shared folders and clipboard sharing to what you need.

---

## 🗺️ Roadmap

- [x] Kali Linux VM with working NAT networking
- [ ] Capture clipboard, drag-and-drop and shared folder evidence
- [ ] Take and document a baseline snapshot
- [ ] Add a Windows 10 target VM
- [ ] Add an Android-x86 target VM
- [ ] Ping-test all machines against each other
- [ ] Begin reconnaissance and scanning exercises

---

## 📁 Repository Structure

```text
.
├── README.md
├── images/
│   ├── 01-ip-addr.png
│   ├── 02-ip-route.png
│   ├── 03-ping-tests.png
│   └── 04-vm-settings.png
└── report/
    └── Shan_Rasool_WK1-PM1_Lab_Setup_Report.pdf
```

---

## 💡 Key Learnings

- How a NAT gateway shares one connection with many private VMs.
- How to read `ip addr` and `ip route` output to confirm network state.
- Why pinging a hostname tests DNS as well as connectivity.
- Why the NAT gateway address (here `10.0.0.2`) cannot be reused by a VM.
- Why snapshots matter before any security testing.

---

## ⚠️ Disclaimer

This project is for **educational purposes only**. Use the tools and techniques in this lab only on systems you own or are explicitly authorized to test. The author accepts no responsibility for misuse.

---

## 👤 Author

**Shan Rasool**
Cybersecurity Intern, Networkwalks Academy


---

<div align="center">

⭐ If this guide helped you, consider starring the repository.

</div>
