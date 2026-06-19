# SOC Architecture — Mini Sentrix

Excalidraw live link: [https://excalidraw.com/\#json\=zT1esMgZod-sJwNVimpoX,1xYQ49oPJojndauB-jmzTg
](https://excalidraw.com/#json=Lu9fP0xAqCIFWVm-kGIC5,zec2OqrFYE3tl2FzhOUxmA)
---

## System Overview

This is a Mini SOC (Security Operations Center) built on a host-only virtual network with zero internet dependency. All VMs and Docker containers communicate internally on `192.168.56.0/24`.

---

## Architecture Layers

### Layer 1 — Perimeter & Detection
- **Suricata IDS** runs on VM 3 in AF_PACKET mode, monitoring all traffic on the virtual interface
- **ModSecurity + Nginx WAF** sits in front of DVWA on VM 2, blocking SQLi, XSS, and brute force attempts
- **Wazuh Agent** runs on VM 3, collecting host-level logs and shipping them to Wazuh Manager

### Layer 2 — SIEM & Visibility
- **Wazuh Manager** receives alerts from all agents, correlates them, and forwards to Elasticsearch
- **Elasticsearch** stores all log data and alerts as indexed JSON documents
- **Kibana / Wazuh Dashboard** provides live visual panels — alert counts, top source IPs, WAF blocks, auth events

### Layer 3 — Identity & Access (IAM)
- **Keycloak** enforces SSO + RBAC + MFA across all internal services
- No one accesses Kibana, TheHive, or Shuffle without authenticating through Keycloak first

### Layer 4 — Automated Response (SOAR)
- **Shuffle** listens for Wazuh webhooks and triggers automated playbooks
- On alert: blocks attacker IP via iptables, creates a case in TheHive, sends notification
- **TheHive** stores all incident cases with full evidence — zero human intervention required

---

## Attack Flow (End-to-End)

Kali Linux (VM1)

│

│ launches attack (Nmap / Hydra / SQLMap)

▼

DVWA / Metasploitable2 (VM2)

│

│ traffic flows through virtual network

▼

Suricata IDS (VM3)

│

│ detects malicious pattern → writes alert to eve.json

▼

Wazuh Agent (VM3)

│

│ ships eve.json to Wazuh Manager (Docker on host)

▼

Wazuh Manager

│

├──► Elasticsearch → Kibana (alert visible on dashboard)

│

└──► Shuffle SOAR (webhook trigger)

│

├──► iptables rule → Kali IP blocked

└──► TheHive case created (incident logged)
---

## VM Configuration

| VM | Role | IP |
|---|---|---|
| VM 1 | Kali Linux (Attacker) | 192.168.56.101 |
| VM 2 | DVWA / Metasploitable2 (Target) | 192.168.56.102 |
| VM 3 | Suricata + Wazuh Agent (Detection) | 192.168.56.103 |
| Host | Docker — Wazuh + ELK + Keycloak + Shuffle + TheHive | 192.168.56.1 |

---

## Tool Stack & Reasoning

| Tool | Layer | Why Chosen |
|---|---|---|
| Suricata | Detection | AF_PACKET mode works on virtual interfaces — no promiscuous mode needed at SRM |
| Wazuh + ELK | SIEM | Single Docker Compose deploy, no external connectivity needed |
| ModSecurity + Nginx | WAF | Fully local, blocks web attacks, logs flow into SIEM |
| Keycloak | IAM | Full SSO + RBAC in Docker, richer than Authelia |
| Shuffle | SOAR | Visual playbook builder, webhook-triggered, no-code automation |
| TheHive | Case Mgmt | Auto-created cases complete the response chain with zero human input |
| DVWA | Target | Deliberately vulnerable, safe to attack locally |

