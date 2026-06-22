# Tool Reasoning — Mini Sentrix SOC

## Network
- **Host-Only Network (VMware Fusion):** Avoids SRM's promiscuous mode restrictions on shared physical interfaces. All VM-to-VM traffic stays internal — zero external internet dependency after setup.
- **Not Bridged:** Bridged mode exposes VMs to SRM's restricted network — packet sniffing blocked.

## IDS — Suricata
- **Why Suricata:** Multi-threaded, runs in AF_PACKET mode on virtual interfaces. No promiscuous mode needed. Lower alert latency than Snort in multi-core environments.
- **Not Snort:** Single-threaded by default, slower on modern hardware.
- **Rules:** Emerging Threats Open ruleset (66,740 rules, 50,815 enabled) + custom local.rules for SYN-flood/port-scan detection.

## SIEM — Wazuh + ELK
- **Why Wazuh:** Native ARM64 agent available. Single Docker Compose deploy. Built-in agent-manager architecture ships logs automatically. No external connectivity needed.
- **Not Splunk:** Paid product, violates hackathon rules.
- **Not pure ELK:** Wazuh adds correlation, rule-based alerting, and agent management on top of ELK — more complete out of the box.

## WAF — Nginx + ModSecurity
- **Why Nginx + ModSecurity:** Fully local Docker-deployable. OWASP CRS gives 922 production-grade rules covering SQLi, XSS, path traversal, command injection.
- **Not CloudFlare/AWS WAF:** Requires internet exposure — not possible on SRM network.
- **Confirmed:** 403 blocks on SQL injection attempts via OWASP CRS rule match.

## Target — DVWA
- **Why DVWA:** Deliberately vulnerable — safe and legal to attack locally. Supports all required attack types: SQL injection, brute force, XSS.
- **Not Metasploitable2:** No ARM64 image available — incompatible with Apple Silicon.
- **Not a real app:** Illegal and unethical to attack production systems.

## IAM — Keycloak (Day 2)
- **Why Keycloak:** Full SSO + RBAC in Docker. Richer role management than Authelia.
- **Not Authelia:** Limited RBAC compared to Keycloak for multi-service environments.

## SOAR — Shuffle (Day 3)
- **Why Shuffle:** Visual playbook builder, webhook-based triggers from Wazuh. No-code automation — faster to demo live than custom scripts.
- **Not custom scripts:** Harder to demo visually to judges in real time.
