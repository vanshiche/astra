# Mini Sentrix — Hackathon Environment Setup

## Overview

This README documents the virtualized environment built for the **Mini Sentrix Hackathon** — a 4-day build of a functional Mini SOC (Security Operations Center) covering Perimeter & Detection, SIEM & Visibility, Identity & Access, and Automated Response, all running locally inside the SRM university network.

---

## Architecture

```
Laptop (Host)
├── VM 1 — Attacker Machine (Kali Linux)
├── VM 2 — Target / Vulnerable App (Metasploitable2 / DVWA)
├── VM 3 — Security Stack (Suricata + Wazuh Agent)
└── Docker on Host — Wazuh Manager + ELK + Shuffle + Keycloak
```

All four layers (Perimeter & Detection, SIEM & Visibility, Identity & Access, Automated Response) run across these VMs and host-level Docker containers, connected through the network setup described below.

---

## Network Adapter Setup

We used **three separate virtual network adapters** in VMware, each serving a distinct purpose. This was necessary because SRM's network restricts raw packet sniffing, external VPNs, and internet-facing ports — so internal VM-to-VM traffic, internet access for downloads, and external team access all had to be handled differently.

### 1. VMnet8 — NAT Adapter (Internet Access for Downloads)
- Used to give VMs outbound internet access **only** for pulling Docker images, installers, and packages (DockerHub, PyPI, npm, apt repos).
- Critical for the Day 1 priority task: pre-downloading all Docker images and VM ISOs before the network got congested.
- Not used for inter-VM attack traffic — kept isolated to package/image retrieval so it didn't interfere with the host-only simulation network.

### 2. VMnet2 — Host-Only Adapter (Isolated Lab Network)
- This is the **primary simulation network** connecting the Attacker VM, Target VM, and Security Stack VM to each other.
- Fully isolated from the outside internet — no external dependency once setup was complete, satisfying the "zero external internet dependency after setup" requirement.
- All attack simulations (Nmap scans, Hydra brute force, SQLMap, etc.) and all detection (Suricata in AF_PACKET mode) ran over this network.
- VMs were configured with static or DHCP-assigned IPs on this segment so Suricata, Wazuh agents, and the WAF could all observe and log traffic consistently.

### 3. VMnet0 — Bridged Adapter (Team Remote Access)
- Bridged directly to the laptop's physical network adapter.
- This allowed teammates to remotely access the VM (e.g., via SSH, RDP, or browser-based dashboards like Kibana/Grafana) over the local SRM LAN, so all 4 team members could view/interact with the system without crowding around one laptop.
- Used **only** for team collaboration access — not for attack traffic or core simulation, keeping the isolated lab (VMnet2) clean and consistent with the "host-only, zero external dependency" architecture.

### Why three adapters instead of one
| Adapter | Type | Purpose | Internet Access |
|---|---|---|---|
| VMnet8 | NAT | Downloading Docker images, ISOs, packages | Yes |
| VMnet2 | Host-Only | Isolated attack/defense simulation network | No |
| VMnet0 | Bridged | Remote dashboard/VM access for teammates | Via SRM LAN |

Each VM was given **multiple network adapters** (NAT + Host-Only, and Bridged where remote access was needed) rather than relying on one adapter for everything. This kept the simulation traffic clean and isolated while still allowing setup-phase downloads and live team access.

---

## VM Inventory

| VM | Role | OS | Adapter(s) |
|---|---|---|---|
| VM 1 | Attacker | Kali Linux | VMnet2 (lab) + VMnet8 (downloads) |
| VM 2 | Target | Metasploitable2 / DVWA | VMnet2 (lab) + VMnet8 (downloads) |
| VM 3 | Security Stack | Suricata + Wazuh Agent | VMnet2 (lab) + VMnet8 (downloads) + VMnet0 (remote dashboard access) |
| Host | Docker Host | Wazuh Manager, ELK, Shuffle, Keycloak | VMnet8 (downloads) + VMnet0 (remote access) |

---

## Setup Steps

1. **Create VMs**: Provision Kali (Attacker), Metasploitable2/DVWA (Target), and a Security Stack VM (Suricata + Wazuh Agent).
2. **Attach adapters**:
   - Add VMnet8 (NAT) to each VM temporarily for downloading dependencies.
   - Add VMnet2 (Host-Only) to each VM as the permanent simulation network.
   - Add VMnet0 (Bridged) to the host/dashboard-facing VM so teammates can reach Kibana/Grafana/Wazuh dashboard remotely.
3. **Pre-download everything (Day 1 priority)**: While on VMnet8, pull all required Docker images (Wazuh, ELK, Grafana, Keycloak, Shuffle, TheHive) and cache installers locally before the SRM network gets congested.
4. **Disable/limit VMnet8 once setup is done** (optional) to fully isolate the simulation network and confirm zero external dependency.
5. **Verify connectivity on VMnet2**: Confirm all lab VMs can ping each other.
6. **Verify remote access on VMnet0**: Confirm teammates on the same SRM LAN can reach the dashboard/VM via the bridged IP.
7. **Deploy stack via Docker Compose** on host: Wazuh Manager, ELK, Shuffle, Keycloak.
8. **Run first attack simulation**: Nmap scan from Kali VM (VMnet2) against Target VM, confirm Suricata fires an alert and it lands in the SIEM.

---

## Notes / Gotchas

- Keep NAT (VMnet8) traffic separate from Host-Only (VMnet2) traffic — mixing them risks attack simulations leaking onto the internet-facing adapter or download traffic polluting IDS logs.
- The bridged adapter (VMnet0) exposes the VM to the wider SRM LAN, so only enable it on the VM(s) that actually need to serve dashboards to teammates — not on the Attacker VM.
- Re-check firewall rules on Host-Only and Bridged adapters separately, since Windows/macOS host firewalls can block inbound bridged traffic by default.
