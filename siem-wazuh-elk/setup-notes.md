# Wazuh SIEM Setup Notes
## Member 3 | Mini Sentrix Hackathon | Day 1 → Day 3

---

## Day 1 — Initial Setup

- Deployed Wazuh Manager + Indexer + Dashboard via official Docker Compose (v4.9.0)
- Hit a cert-generation bug: SSL cert files were created as empty directories instead
  of real files, causing the indexer to crash-loop. Fixed by deleting the
  wazuh_indexer_ssl_certs folder and regenerating via:
  docker compose -f generate-indexer-certs.yml run --rm generator
- Agents connect over Wi-Fi (host IP 10.9.210.29) since team laptops are separate
  machines, not a shared VirtualBox host-only network
- Opened firewall ports 1514, 1515, 55000 for inbound agent traffic
- Confirmed: 1 agent (Ubuntu VM) showing Active status with real telemetry flowing
  (50 medium + 150 low severity alerts from baseline system monitoring)

---

## Day 2 — Agent Expansion & IDS Integration

- Connected Suricata IDS logs into Wazuh via Wazuh agent on Ubuntu Security VM
- Suricata log path configured: /var/log/suricata/eve.json (JSON format)
- Verified Suricata alerts appear in Wazuh dashboard under rule.groups: suricata
- Expanded to 4 active Wazuh agents:
  | Agent ID | Name | IP | OS |
  |----------|------|----|----|
  | 002 | amal-VMware20-1 | 192.168.82.131 | Ubuntu 25.10 |
  | 004 | Vansh-laptop | 10.9.15.118 | Windows 11 |
  | 007 | root | 10.9.26.170 | Ubuntu 24.04 LTS |
  | 008 | Peter_Parker | 10.9.210.4 | Windows 11 |
- Built initial dashboard with Alert Count, Top Source IPs, Auth Events, Critical Events panels

---

## Day 3 — WAF Integration & Full Dashboard

### WAF (ModSecurity) Log Integration
- Teammate (Member 4) installed Wazuh agent on Nginx/WAF machine
- ModSecurity log path configured: /var/log/nginx/modsec_audit.log
- Log format: apache
- Verified WAF events appear in Wazuh as a SEPARATE source from Suricata
- WAF filter: rule.groups: modsecurity

### Dashboard — All 5 Panels Completed
| Panel | Field | Visualization |
|-------|-------|--------------|
| Alert Count by Type | rule.description | Bar chart |
| Top Source IPs | agent.ip | Bar chart |
| Authentication Events | rule.groups | Bar chart |
| Critical Security Events | rule.level | Donut chart |
| WAF Blocks | rule.groups: modsecurity | Bar chart |

### Attacks Detected & Verified in Dashboard
| Attack | Tool | Detected By | SIEM Visible |
|--------|------|-------------|--------------|
| Port Scan | Nmap | Suricata | Yes |
| Brute Force | Hydra | Suricata | Yes |
| SQL Injection | SQLMap | ModSecurity + Suricata | Yes — 2,576 hits |

### Key Evidence
- 129 Suricata hits confirmed (rule.groups: suricata filter)
- 2,576 WAF hits confirmed including:
  - WAF (ModSecurity) blocked a malicious request (rule level 10)
  - SQL injection attempt (rule level 6)
  - ModSecurity rejected a query (rule level 7)
- 4 active agents all reporting in real time
- Dashboard auto-refresh set to every 10 seconds

### Data Flow
Suricata → /var/log/suricata/eve.json → Wazuh Agent (root VM) → Wazuh Manager → OpenSearch → Dashboard

ModSecurity → /var/log/nginx/modsec_audit.log → Wazuh Agent (Vansh-laptop) → Wazuh Manager → OpenSearch → Dashboard
### Firewall Ports Open
| Port | Protocol | Purpose |
|------|----------|---------|
| 1514 | UDP/TCP | Wazuh agent communication |
| 1515 | TCP | Wazuh agent enrollment |
| 55000 | TCP | Wazuh REST API |

---

## Day 3 Success Criteria — All Met
- [x] Suricata alerts visible in Wazuh dashboard
- [x] WAF events visible as separate source
- [x] All 3 attacks visible in dashboard
- [x] 5 dashboard panels live
- [x] Real-time monitoring active
- [x] 4 agents connected and reporting
