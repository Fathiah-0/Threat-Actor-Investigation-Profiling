# Threat Actor Investigation & Profiling: El Machete
### MISP-Based Cyber Threat Intelligence | National CERT Simulation

---

## Executive Summary

This project documents a hands-on threat intelligence investigation conducted 
as part of a national CERT simulation exercise. The working environment was 
built on Kali Linux using Docker to deploy MISP (Malware Information Sharing 
Platform) locally.

Default threat intelligence feeds were loaded, a custom FireHOL Malicious IP 
feed was configured, and event data was reviewed through the MISP web interface.

The intelligence gathered points strongly to El Machete (APT-C-43 / G0095), 
a long-running Spanish-speaking cyber espionage group assessed to be based in 
Latin America. The group is characterized by highly targeted spear-phishing, 
disguised .scr and archive files, Python-based spyware, long dwell time, and 
sustained data exfiltration over FTP and HTTP channels.

Overall threat level assessed as: **Medium to High** — particularly for public 
sector, critical infrastructure, and government organizations.
