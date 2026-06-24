# ASTRA — Mini Sentrix SOC

A connected, automated, open-source Security Operations Center built for the Mini Sentrix Hackathon.
4 days | 5 members | 100% local | Zero external dependencies

---

## What We Built

A fully functional Mini SOC with 4 connected layers:

| Layer | Tool | Status |
|---|---|---|
| Perimeter & Detection | Suricata IDS | ✅ Live |
| SIEM & Visibility | Wazuh + OpenSearch Dashboard | ✅ Live |
| Identity & Access | Keycloak (SSO + RBAC) | ✅ Live |
| Automated Response | Shuffle SOAR + iptables | ✅ Live |

---

## Network Architecture
Kali Linux (Attacker)

↓

Host-Only Network

↓

Ubuntu Security VM — 10.9.26.170 (Suricata IDS + Wazuh Agent)

↓

Wazuh Manager (Docker on Mac)

↓

Wazuh Dashboard (OpenSearch) → 5-Panel Live Dashboard

↑

Vansh-laptop — 10.9.15.118 (ModSecurity WAF + Nginx → DVWA)

↑

Metasploitable2 — 192.168.100.20 (Target)
### Monitored Agents
- `root` — Ubuntu 24.04.4 LTS — 10.9.26.170 (IDS + Wazuh Agent)
- `Vansh-laptop` — Windows 11 — 10.9.15.118 (WAF)
- `amal-VMware20-1` — Ubuntu 25.10 — 192.168.82.131
- `Peter_Parker` — Windows 11 — 10.9.210.4

---

## Repository Structure
astra/

├── architecture/              # Architecture diagram + docs

├── docs/                      # Setup and reference docs

├── iam/                       # Keycloak config

├── ids-suricata/              # Suricata rules and config

├── siem-wazuh-elk/            # Wazuh docker-compose + setup

│   └── screenshots/

│       ├── day3/              # Day 3 dashboard + attack proofs

│       └── day4/              # Day 4 brownie point + response proofs

├── soar-response/             # Shuffle playbooks + block scripts

└── README.md
---

## Day-by-Day Progress

### Day 1 — Foundation & Architecture
- All VMs created and connected on host-only network
- Suricata IDS installed and running on Ubuntu VM
- Wazuh Manager + Dashboard deployed via Docker Compose
- First Nmap scan triggered Suricata alert — confirmed in SIEM
- Architecture diagram finalized

**Brownie Point:** The Sleepwalker's Diary
- Demonstrated alert correlation across time in Wazuh
- Live Nmap SYN scan (-sS -T4) against 192.168.100.20
- 658+ Suricata detections/second, 13,483 alerts correlated in SIEM

### Day 2 — Core Integrations
- ModSecurity + Nginx WAF deployed on Vansh-laptop (Docker)
- WAF sitting in front of DVWA on port 80→8080
- All 3 attack simulations run: Nmap, Hydra, SQLMap
- WAF blocked SQLMap — 403 response confirmed
- WAF logs flowing into Wazuh as separate source from IDS
- SIEM dashboard built with data from 2 sources

**Brownie Point:** The Loyal Guard Who Lets Everyone In
- Keycloak deployed with SSO + RBAC (admin + analyst roles)
- Login event vs login pattern — anomalous access pattern detection configured

### Day 3 — Automation & Response
- Shuffle SOAR deployed via Docker Compose
- Automated playbook: Hydra attack → Suricata alert → Wazuh → Shuffle → iptables block
- Full pipeline tested end-to-end — zero human intervention after attack launch
- 5-panel SIEM dashboard completed and saved as pinned demo view
- Keycloak protecting internal service — unauthenticated access denied

**Brownie Point:** The Alarm That Cried Wolf
- Custom correlation rule (rule.id: 100460) — "Custom Correlation Layer: Attack Campaign Detection" (level 12)
- Wazuh frequency rules differentiate noise (level 3) from critical events (level 10)
- Same source IP, different weight based on cumulative behavior pattern

### Day 4 — Final Demo Day
- All VMs rebooted and confirmed stable
- Full pipeline dry-run completed and timed
- Dashboard auto-refresh set to 5 seconds — pinned saved view locked
- Architecture summary document prepared for judges
- All 3 attacks confirmed producing clean alerts in final demo

**Brownie Point:** The Map That Only Shows Roads You Have Already Walked
- Anomaly detection layer built on top of signature-based IDS
- Custom rule (rule.id: 100470) — "Anomaly Detection Layer: Behavioral Deviation" (level 13)
- Detects statistically abnormal behavior even when no signature matches
- Demonstrated: slow/modified attack caught by anomaly layer where signature rules missed it
- Automated response confirmed: POST /block_ip firing repeatedly from 10.9.26.170

---

## SIEM Dashboard — 5 Panels

| Panel | What It Shows |
|---|---|
| Alert Count by Type | Event counts grouped by rule.description |
| Top Source IPs | Highest-volume source IPs by alert count |
| WAF Blocks | ModSecurity blocked + rejected events |
| Authentication Events | Keycloak login events by rule group |
| Critical Security Events | rule.level 7 vs 10 distribution (donut) |

---

## Attack Simulations

| Attack | Tool | Alert | Response |
|---|---|---|---|
| Port Scan | nmap -sS -T4 | Suricata: ET INFO Possible Kali Linux host scan | Alert in Wazuh |
| Brute Force | Hydra | Suricata brute force + auth failure | Shuffle → iptables block |
| SQL Injection | SQLMap | WAF: ModSecurity blocked malicious request (rule 100100) | 403 + Wazuh event |

---

## Tool Stack

| Layer | Tool | Why |
|---|---|---|
| IDS | Suricata | AF_PACKET on virtual interface — SRM network compatible |
| WAF | ModSecurity + Nginx | Fully local Docker deployment |
| SIEM | Wazuh + OpenSearch | Docker Compose, agent-based, no external connectivity |
| Dashboard | Wazuh Dashboard | Built-in, browser-accessible, 5-panel custom view |
| IAM | Keycloak | Local Docker, OAuth2/OIDC, SSO + RBAC |
| SOAR | Shuffle | Docker Compose, visual playbook builder |
| Attack | Kali Linux VM | Standard penetration testing platform |
| Target | Metasploitable2 + DVWA | Deliberately vulnerable local targets |

---

## Brownie Points Summary

| Day | Riddle | What We Built | Rule ID |
|---|---|---|---|
| Day 3 | The Alarm That Cried Wolf | Custom correlation layer — alert prioritization by cumulative context | 100460 |
| Day 4 | The Map That Only Shows Roads You Have Already Walked | Anomaly detection layer — behavioral deviation from statistical baseline | 100470 |

---

*Mini Sentrix Hackathon | Powered by Talenciaglobal | Team ASTRA*
