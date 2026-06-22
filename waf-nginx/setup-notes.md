# WAF Setup — Nginx + ModSecurity

## What it does
Nginx runs on port 8888, proxying all traffic to DVWA (port 4280).
ModSecurity with OWASP CRS (922 rules) sits inline — blocking SQL injection, XSS, and other attacks before they reach DVWA.

## Why Nginx + ModSecurity
- Fully local, no internet needed after install
- OWASP CRS covers all attack types needed for this hackathon (SQLi, XSS, path traversal)
- ModSecurity v3 integrates natively with Nginx via libnginx-mod-http-modsecurity

## Port layout
- Port 4280: DVWA direct (no WAF) — used internally
- Port 8888: DVWA via WAF (Nginx + ModSecurity) — used for all demo attacks

## ModSecurity config
- Engine: ON (blocking mode, not detection-only)
- Rules: OWASP CRS 3.3.7 — 922 rules loaded
- Config: /etc/nginx/modsecurity.conf
- CRS setup: /etc/modsecurity/crs/crs-setup.conf
- CRS rules: /usr/share/modsecurity-crs/rules/*.conf

## Confirmed blocking
SQL injection test: http://192.168.82.131:8888/\?id\=1'+OR+'1'='1
Result: 403 Forbidden — blocked by ModSecurity OWASP CRS
