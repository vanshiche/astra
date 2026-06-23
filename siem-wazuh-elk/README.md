# SIEM & Dashboard Layer — Wazuh + OpenSearch
## Member 3 | Mini Sentrix Hackathon | Day 3

---

## What This Layer Does
This is the SIEM (Security Information and Event Management) layer of our Mini SOC.
It collects logs from all layers, correlates events, and displays them on a live dashboard.

---

## Stack Used
| Tool | Purpose |
|------|---------|
| Wazuh Manager | Log collection, rule engine, alert generation |
| OpenSearch | Log storage and indexing |
| Wazuh Dashboard | Live visualization and monitoring |

---

## Log Sources Integrated
| Source | Type | Agent |
|--------|------|-------|
| Suricata (IDS) | Network intrusion alerts | Wazuh agent on Ubuntu Security VM |
| ModSecurity (WAF) | Web attack blocks | Wazuh agent on Nginx/WAF machine |

---

## Dashboard Panels Built
| Panel | Field Used | Purpose |
|-------|-----------|---------|
| Alert Count by Type | rule.description | Shows distribution of alert types |
| Top Source IPs | agent.ip | Identifies top attacking IPs |
| Authentication Events | rule.groups: auth | Tracks login events |
| Critical Security Events | rule.level | Highlights high severity events |
| WAF Blocks | rule.groups: modsecurity | Shows blocked web attacks |

---

## Attacks Detected & Visible in Dashboard
| Attack | Tool Used | Detected By | Visible in SIEM |
|--------|----------|-------------|-----------------|
| Port Scan | Nmap | Suricata | Yes |
| Brute Force | Hydra | Suricata | Yes |
| SQL Injection | SQLMap | ModSecurity + Suricata | Yes |

---

## How Logs Flow Into Wazuh
Suricata → eve.json → Wazuh Agent → Wazuh Manager → OpenSearch → Dashboard

ModSecurity → modsec_audit.log → Wazuh Agent → Wazuh Manager → OpenSearch → Dashboard
---

## Configuration
- Suricata log path: `/var/log/suricata/eve.json`
- ModSecurity log path: `/var/log/nginx/modsec_audit.log`
- Log format: JSON
- Dashboard auto-refresh: every 10 seconds
- Active Wazuh agents: 4 (amal-VMware20-1, Vansh-laptop, root, Peter_Parker)

---

## Screenshots
| File | Description |
|------|-------------|
| 01_alert_count_by_type.png | Dashboard panel — alert distribution |
| 02_top_source_ips.png | Dashboard panel — top attacker IPs |
| 03_authentication_events.png | Dashboard panel — auth events |
| 04_critical_security_events.png | Dashboard panel — critical events |
| 05_waf_blocks.png | Dashboard panel — WAF blocks |
| 06_suricata_filter_proof.png | Suricata as separate source in SIEM |
| 07_modsecurity_filter_proof.png | ModSecurity as separate source in SIEM |
| 08_full_dashboard.png | Full dashboard — all 5 panels live |
| 09_waf_modsecurity_live_detections.png | WAF blocking SQLMap attack live |
| 10_wazuh_agents_active.png | 4 active Wazuh agents connected |
| 11_suricata_alerts_filtered.png | 129 Suricata hits filtered by rule group |

---

## Day 3 Success Criteria — All Met
- [x] Suricata alerts visible in Wazuh dashboard
- [x] WAF events visible in Wazuh dashboard
- [x] IDS and WAF appear as separate data sources
- [x] All 3 attacks visible in dashboard
- [x] 5 dashboard panels built and live
- [x] Real-time monitoring with auto-refresh
- [x] 4 Wazuh agents active and reporting
