# Day 2 - SIEM & Dashboard Engineer (Member 3)

## Completed Tasks

### 1. Suricata → Wazuh Integration
- Configured `/var/ossec/etc/ossec.conf` on Ubuntu VM with localfile block
- log_format: json, location: /var/log/suricata/eve.json
- Restarted wazuh-agent to pick up new config
- Verified Suricata alerts (ET SCAN Nmap) appearing in Wazuh dashboard

### 2. WAF Logs Integration
- Added ModSecurity JSON decoder to Wazuh Manager
- Enabled logall_json on Wazuh Manager for archive indexing
- Verified WAF block events (rule 913100 - sqlmap scanner) in archives
- WAF agent: Vansh-laptop (Windows, 10.9.15.118) - Active

### 3. Dashboard Built
- 4 panels created in Mini SOC Security Dashboard:
  - Alert Count by Type (rule.description)
  - Top Source IPs (agent.ip)
  - Authentication Events (rule.groups)
  - Critical Security Events (rule.level pie chart)

### 4. Two Sources Confirmed
- Source 1: amal-VMware20-1 (Ubuntu VM) - Suricata IDS alerts
- Source 2: Vansh-laptop (Windows) - WAF ModSecurity events

## Evidence
- Screenshots in /screenshots folder
