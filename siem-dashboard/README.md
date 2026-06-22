# Day 2 – SIEM & Dashboard Engineer (Member 3)

## Role Overview
Responsible for configuring Wazuh, managing the OpenSearch stack, integrating log sources, and building the live security dashboard.

---

## Task 1: Suricata → Wazuh Integration ✅

### What was done:
- Configured `/var/ossec/etc/ossec.conf` on the Ubuntu VM (amal-VMware20-1) with a localfile block to ingest Suricata eve.json logs
- Set `log_format: json` and `location: /var/log/suricata/eve.json`
- Restarted wazuh-agent to apply the new configuration
- Triggered a real Nmap scan from the attacker machine to generate Suricata alerts
- Verified ET SCAN alerts (Nmap detection) appearing live in Wazuh Threat Hunting → Events

### Why this matters:
Suricata generates network-level alerts. Without this integration, IDS alerts would never reach the SIEM — defenders would be blind to network attacks.

### Evidence:
- `screenshots/suricata-alerts-wazuh-dashboard.png` — Suricata alerts visible in Wazuh dashboard

---

## Task 2: WAF Logs Integration ✅

### What was done:
- Added ModSecurity JSON decoder to Wazuh Manager for structured WAF log parsing
- Enabled `logall_json` on Wazuh Manager to ensure WAF events are indexed in archives
- WAF agent (Vansh-laptop, Windows, IP: 10.9.15.118) configured and confirmed Active
- Verified WAF block events (ModSecurity rule 913100 — sqlmap scanner detection) appearing in wazuh-archives index
- Confirmed two separate log sources visible in SIEM: IDS (Suricata) + WAF (ModSecurity)

### Why this matters:
WAF operates at Layer 7 (application layer) while Suricata operates at Layer 3/4 (network layer). Having both in the SIEM gives full-stack visibility — network attacks AND web application attacks in one place.

### Evidence:
- `screenshots/vansh-laptop-waf-agent-events.png` — Vansh-laptop WAF agent events in SIEM
- `screenshots/agents-active.png` — both agents (Ubuntu IDS + Windows WAF) confirmed active

---

## Task 3: SIEM Dashboard Built ✅

### Dashboard name: Mini SOC Security Dashboard

### Panels created:
| Panel | Field Used | Visualization |
|-------|-----------|---------------|
| Alert Count by Type | rule.description | Bar chart |
| Top Source IPs | agent.ip | Bar chart |
| Authentication Events | rule.groups | Bar chart |
| Critical Security Events | rule.level | Pie/Donut chart |

### Why these panels:
- **Alert Count by Type** — identifies what kinds of attacks are happening most frequently
- **Top Source IPs** — immediately shows which machines are generating the most noise (attacker IPs surface here)
- **Authentication Events** — tracks login attempts, brute force patterns, privilege escalation
- **Critical Security Events** — severity-based view so SOC analysts triage high-priority alerts first

### Evidence:
- `screenshots/dashboard-4-panels.png` — all 4 panels live with real data

---

## Task 4: Two Log Sources Confirmed ✅

| Agent | Machine | OS | IP | Role | Status |
|-------|---------|----|----|------|--------|
| 002 | amal-VMware20-1 | Ubuntu 25.10 | 192.168.82.131 | Suricata IDS | Active |
| 004 | Vansh-laptop | Windows 11 | 10.9.15.118 | WAF ModSecurity | Active |

### Evidence:
- `screenshots/agents-active.png` — Wazuh Endpoints page showing both agents active

---

## Success Criteria Check

| Criteria | Status | Evidence |
|----------|--------|----------|
| Suricata alerts visible in dashboard | ✅ | suricata-alerts-wazuh-dashboard.png |
| WAF events visible in dashboard | ✅ | vansh-laptop-waf-agent-events.png |
| Real-time monitoring demonstrated | ✅ | dashboard-4-panels.png (Last 24 hours live data) |
| Two separate log sources in SIEM | ✅ | agents-active.png |
| Working dashboard with visualizations | ✅ | dashboard-4-panels.png |

---

## Tools Used & Why

| Tool | Why This Tool |
|------|--------------|
| Wazuh Manager | Central log aggregation — Docker deployable, works on ARM under emulation, no external connectivity needed |
| OpenSearch/Wazuh Indexer | Stores and indexes all security events — replaces ELK, bundled with Wazuh 4.9 |
| Wazuh Dashboard | Built-in Kibana-based UI — allows custom dashboards without extra setup |
| ModSecurity decoder | Parses WAF JSON logs into structured Wazuh fields for proper alerting |

---

## Architecture (Member 3 view)
Ubuntu VM (amal-VMware20-1)          Windows (Vansh-laptop)

Suricata → eve.json                  ModSecurity WAF logs

↓                                      ↓

Wazuh Agent                          Wazuh Agent

↓                                      ↓

└──────────→ Wazuh Manager ←───────────┘

↓

OpenSearch Indexer

↓

Wazuh Dashboard

(Mini SOC Security Dashboard)
