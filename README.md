# 🛡️ Personal SOC & CyberSecurity Homelab

## Overview
This repository documents the architecture, configuration, and deployment of my personal, fully isolated CyberSecurity Home Lab. The environment was designed to simulate enterprise infrastructure for Blue Teaming (SOC operations), Malware Analysis, and Active Directory security testing.

## Hardware Infrastructure
The environment utilizes a high-performance workstation for virtualization and a dedicated bare-metal desktop for administration:
* **Hypervisor:** Proxmox VE 8 (Hosted on HP Z440 Workstation - Intel Xeon, 64GB RAM, NVMe/SSD Storage). This single node hosts all virtual machines and networks, including the TrueNAS storage server.
* **Management Station (Daily Driver):** Dell Optiplex running Ubuntu Desktop. Used as the primary administrative console for infrastructure management, secure SSH access, documentation, and SIEM log analysis.

## Network Topology & Segmentation
The network is logically divided into two distinct segments using a virtualized **pfSense** firewall/router. This ensures strict isolation of the SOC Lab (where malware and attacks are detonated) from the private home network.

* **`vmbr0` (Home LAN - 192.168.1.0/24):** The primary network with internet access. Hosts the offensive machines (Red Team) and core services (like NAS).
* **`vmbr1` (SOC Lab LAN - 10.0.0.0/24):** The isolated "sandbox" environment. All outbound traffic to the home network is explicitly blocked via pfSense firewall rules. 

> <img width="872" height="383" alt="Homelab architecturev2" src="https://github.com/user-attachments/assets/b3567119-9a24-4d39-938a-d8588fa72ae4" />


## 🖥️ Virtual Machines Inventory (Infrastructure as Code)

The following table details the virtual machine configurations, extracted directly from Proxmox `.conf` files.

| VM ID | Hostname | OS | vCPU | RAM | Network (Bridge) | IP Address | Role / Description |
|---|---|---|---|---|---|---|---|
| **101** | `pfSense-Router` | pfSense CE 2.6.0 | 2 | 2 GB | `vmbr0`, `vmbr1` | `192.168.1.x` / `10.0.0.x` | Virtual Firewall/Router. Enforces network isolation. |
| **105** | `SIEM` | Ubuntu Server 26.04 | 4 | 8 GB | `vmbr1` | `10.0.0.12` | Centralized log management and monitoring (Wazuh SIEM). |
| **102** | `AD-Server-2025` | Windows Server 2025| 4 | 12.5 GB| `vmbr1` | `10.0.0.x` | Domain Controller (Active Directory) for the isolated network. |
| **104** | `Win11Victim` | Windows 11 25H2 | 4 | 6.5 GB | `vmbr1` | `10.0.0.x` | Domain-joined endpoint (Target for attack simulations). |
| **103** | `Kali-Attacker`| Kali Linux 2026.2 | 4 | 7 GB | `vmbr0` | `192.168.1.200` | Offensive operations machine (Red Teaming). |
| **106** | `TrueNAS` | TrueNAS SCALE | 4 | 12 GB | `vmbr0` | `192.168.1.x` | Virtualized local file server and backup storage. |
| **100** | `FFAStrans` | Windows Server 2022| 14 | 8 GB | `vmbr0` | `192.168.1.x` | Utility server (FFAStrans) / Target in the LAN zone. |

## 🛠️ Key Technologies & Tool Stack
* **SIEM / EDR:** Wazuh (OpenSearch, Filebeat)
* **Firewall / Routing:** pfSense (Stateful Packet Inspection)
* **Virtualization:** Proxmox VE (QEMU/KVM)
* **Identity & Access Management:** Microsoft Active Directory

---
*Disclaimer: This documentation is created for cybersecurity portfolio purposes. It contains only RFC 1918 private IP addresses. No sensitive personal data or public IPs are exposed.*
