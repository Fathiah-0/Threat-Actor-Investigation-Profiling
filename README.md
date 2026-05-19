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

```bash
# Install Docker packages
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

```

<img width="3840" height="2400" alt="Screenshot 2026-02-24 162232" src="https://github.com/user-attachments/assets/f65cd8dd-c143-48f4-a34a-d1e5c4b0ff54" />

<img width="3840" height="2400" alt="Screenshot 2026-02-24 162256" src="https://github.com/user-attachments/assets/8cfafbb5-9df9-42af-ac39-076da0db1ae2" />



### 2. Clone MISP Docker Repository
```bash
sudo apt install git
git clone https://github.com/MISP/misp-docker
cd misp-docker/
cp template.env .env
```

<img width="3840" height="2400" alt="Screenshot 2026-02-24 162631" src="https://github.com/user-attachments/assets/de35da34-f610-4a45-b28c-f4add7f5bfe2" />

```bash
# Pull the images
sudo docker compose pull

# Start MISP
sudo docker compose up -d
```

<img width="3840" height="2400" alt="Screenshot 2026-05-19 170955" src="https://github.com/user-attachments/assets/3d249763-5861-41b9-a2cb-a6bdd87b6ed3" />

### 4. Access MISP

Once all containers are healthy, access MISP via browser:
- URL: `http://localhost`
- Email: `admin@admin.test`
- Password: `admin`

> Note: During deployment, temporary warnings involving misp-modules and the 
> database service were observed. Despite these, the MISP web interface became 
> fully accessible, confirming successful deployment.


<img width="3840" height="2400" alt="Screenshot 2026-05-19 173120" src="https://github.com/user-attachments/assets/9ce63de8-a09f-49d9-a980-9d9b5211f301" />
