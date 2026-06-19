\# Wazuh SIEM Setup Notes



\- Deployed Wazuh Manager + Indexer + Dashboard via official Docker Compose (v4.9.0)

\- Hit a cert-generation bug: SSL cert files were created as empty directories instead 

&#x20; of real files, causing the indexer to crash-loop. Fixed by deleting the 

&#x20; wazuh\_indexer\_ssl\_certs folder and regenerating via:

&#x20; docker compose -f generate-indexer-certs.yml run --rm generator

\- Agents connect over Wi-Fi (host IP 10.9.210.29) since team laptops are separate 

&#x20; machines, not a shared VirtualBox host-only network

\- Opened firewall ports 1514, 1515, 55000 for inbound agent traffic

\- Confirmed: 1 agent (Ubuntu VM) showing Active status with real telemetry flowing

&#x20; (50 medium + 150 low severity alerts from baseline system monitoring)

\- Next: wire in Suricata logs once IDS layer is ready (teammate's task)

