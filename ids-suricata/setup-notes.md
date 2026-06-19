# Suricata Setup — Day 1

**Owner:** Person 1 (IDS Layer)
**VM:** VM3 — Ubuntu Server

## What was done
- Installed Suricata via `apt` on VM3
- Configured AF_PACKET mode on the host-only virtual interface
- HOME_NET left at default (192.168.0.0/16,10.0.0.0/8,172.16.0.0/12) — covers our host-only subnet
- Enabled eve.json logging for alert, dns, http, tls event types
- Started Suricata as a systemd service (`sudo systemctl enable suricata --now`)

## Verification
- Ran Nmap SYN scan from VM1 (Kali) against VM2/VM3
- Confirmed alert events appeared live in `/var/log/suricata/eve.json`
- Sample alert saved as `sample-eve-alert.json` in this folder

## Why Suricata (tool reasoning)
Suricata supports AF_PACKET mode, which works on host-only virtual network
interfaces without needing promiscuous mode on a shared physical NIC — this
matters because SRM's network restricts promiscuous mode on shared interfaces.
Snort and similar tools that rely on libpcap promiscuous capture would hit
that restriction; Suricata's AF_PACKET socket-based capture does not.