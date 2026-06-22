\# WAF Setup — Day 2



\- Deployed DVWA + ModSecurity/Nginx via Docker Compose on host laptop (Windows)

\- ModSecurity image includes OWASP Core Rule Set, PARANOIA level 1

\- Verified: SQLMap from Kali VM against DVWA blocked - 147 requests returned 403

\- Attacker IP: 172.18.0.1 (Kali via Docker bridge)

\- Rules triggered: 942280, 942100 (SQLi via libinjection), 932380 (RCE pattern), 913100 (scanner UA detection)

\- Inbound anomaly score: 33 (blocking threshold: 5)

\- Why ModSecurity: fully local, Docker-deployable, no external dependency - fits SRM network constraints

