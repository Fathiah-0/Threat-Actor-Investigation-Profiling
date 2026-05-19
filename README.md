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


---

## Methodology & Environment Setup

The investigative environment was prepared on a Kali Linux virtual machine. 
The setup involved the following steps:

### Correct Way to Paste in Your README:

Here is the full clean section again. **Copy everything below** and paste it directly into your README editor:

---

**1. Install Docker**

```bash
# Add Docker's official GPG key
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```bash
# Add Docker's repository
echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] \
https://download.docker.com/linux/debian bookworm stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

<img width="800" alt="Docker Installation on Kali Linux" src="https://github.com/user-attachments/assets/56f228b5-5f40-4e1f-824f-57842f56d741" />



