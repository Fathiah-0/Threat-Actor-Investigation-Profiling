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

## Introduction

Threat intelligence supports defensive security by helping analysts understand 
who an adversary is, how they operate, what infrastructure they use, and what 
indicators can be monitored to detect or prevent compromise.

This project was conducted as part of a national CERT simulation exercise. 
Five teams were each assigned one of five suspected threat actor groups believed 
to be behind a wave of attacks targeting critical infrastructure and government 
agencies in Europe. Our team (Group 2) was assigned El Machete.

The full workflow covers:
- Deployment and configuration of MISP using Docker on Kali Linux
- Loading and enabling real-world threat intelligence feeds
- Extracting and analyzing Indicators of Compromise (IOCs)
- Building a complete threat actor profile for El Machete
- Mapping observed activity to the MITRE ATT&CK framework
- Delivering detection and mitigation recommendations

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

### 3. Access MISP

Once all containers are healthy, access MISP via browser:
- URL: `http://localhost`
- Email: `admin@admin.test`
- Password: `admin`

> Note: During deployment, temporary warnings involving misp-modules and the 
> database service were observed. Despite these, the MISP web interface became 
> fully accessible, confirming successful deployment.


<img width="3840" height="2400" alt="Screenshot 2026-05-19 173120" src="https://github.com/user-attachments/assets/9ce63de8-a09f-49d9-a980-9d9b5211f301" />

---

## MISP Access and Feed Configuration

After deployment, the MISP login page was accessed through http://localhost. 
Default feed metadata was loaded from the Feeds page. Only the first three 
default feeds were enabled. Feed caching was also enabled so that event data 
could be stored locally and reviewed inside the platform.

### Default Feeds

Steps to enable default feeds:
1. Click on **Sync Actions** then select **Feeds**
2. Click **Load Default Feed Metadata**
3. Select the first three feeds and click **Enable Selected**
4. Click **Enable Caching for Selected**
5. Click **Fetch All Events**

<img width="3840" height="2400" alt="Screenshot 2026-05-19 173925" src="https://github.com/user-attachments/assets/49bb52d0-4b89-447f-aa2f-7107ad71ce2d" />

*Figure 5: MISP Feeds page showing enabled default feeds*

---

### Custom Feed — FireHOL Malicious IPs

A custom feed was added with the following configuration:

| Field | Value |
|---|---|
| Feed Name | FireHOL Malicious IPs |
| Provider | FireHOL |
| Input Source | Network |
| URL | https://raw.githubusercontent.com/firehol/blocklist-ipsets/master/firehol_level1.netset |
| Source Format | Freetext |
| Distribution | Your organisation only |

<img width="3840" height="2400" alt="Screenshot 2026-05-19 174124" src="https://github.com/user-attachments/assets/155e11f3-fe27-4cf3-8d8f-872f9de63362" />

*Figure 6: Custom FireHOL Malicious IPs feed configuration*

---

### Custom Tag

A custom tag was created to classify feed data:

- **Tag Name:** `feed-source:firehol`
- **Created under:** Event Actions → Tag Actions → Add Tag
- **Purpose:** Labels events imported from the FireHOL feed for easy filtering

<img width="3840" height="2400" alt="Screenshot 2026-05-19 174850" src="https://github.com/user-attachments/assets/3b357189-3fae-4383-bdfa-94c2b9239c51" />


*Figure 7: Custom tag feed-source:firehol applied to imported events*

---

## Data Collection and IOC Extraction

Once the feeds were configured, events were queried and indicator data was 
extracted through the MISP web interface. The Search Attributes interface was 
used to verify that imported data was queryable and usable for threat analysis.

### Event Creation and Verification

<img width="3840" height="2400" alt="Screenshot 2026-05-19 174850" src="https://github.com/user-attachments/assets/8c8c75ff-5654-4beb-8cef-518b441f1284" />

<img width="3840" height="2400" alt="Screenshot 2026-05-19 175851" src="https://github.com/user-attachments/assets/4b193574-b7ff-42ab-b538-e4c45810a21f" />

*Figure 8: FireHOL Malicious IPs event details showing Event ID 547 and feed-source:firehol tag*

<img width="3840" height="2400" alt="Screenshot 2026-05-19 180645" src="https://github.com/user-attachments/assets/c51a762a-7279-4515-a4b2-3a6039d7ec21" />

*Figure 10: Search Attributes interface used to query indicators inside MISP*

<img width="3840" height="2400" alt="Screenshot 2026-05-19 180718" src="https://github.com/user-attachments/assets/19ab7946-c67a-4017-b36d-59122776fac7" />

<img width="3840" height="2400" alt="Screenshot 2026-05-19 180739" src="https://github.com/user-attachments/assets/24c88718-0874-4fb6-82a9-e6aa85fd2b45" />

<img width="3840" height="2400" alt="Screenshot 2026-05-19 180826" src="https://github.com/user-attachments/assets/b331ed9f-db49-49df-97bd-40ed516a0373" />


*Figure 11: Events list filtered to show El Machete related events*

---

### IOC Summary

| IOC Category | Count |
|---|---|
| Destination IPs | 10 |
| Domains | 3 |
| URLs | 9 |
| Filenames | 37 |
| MD5 Hashes | 99 |
| SHA256 Hashes | 57 |

---

### Key Indicators

**Destination IP Addresses (C2 Infrastructure)**
- 185.224.137.63
- 156.67.222.88
- 158.69.9.209
- 142.44.236.215
- 199.79.63.188
- 109.61.164.33
- 176.9.3.184
- 213.239.232.149
- 69.64.43.33
- 181.50.98.50

**Domains**
- tobabean.expert
- koliast.com
- artyomt.com

**Hostnames**
- jristr.hopto.org
- lawyersofficial.mipropia.com
- mcsi.gotdns.ch
- djcaps.gotdns.ch

**Sample Malicious URLs**
- http://actualizacion.esy.es/Mision_Secreta_de_la_DINA_en_Washigton.rar
- http://cristianoo.esy.es/Padrino_Lopez_Hay_un_golpe_de_Estado_en_desarrollo.zip
- http://informesanddocumentos.esy.es/semanario_en_marcha_1758_1.zip

**Notable Filenames**
- GoogleUpdate.exe / Chrome.exe / GoogleCrash.exe *(masquerading as legitimate software)*
- python27.exe / Python_27.exe
- 977_REG_IN_CO_012_V1.scr
- ORDENES_GENERALES.scr
- Mision_Secreta_de_la_DINA_en_Washigton.scr

---

### Interpretation

The IP addresses are historically associated with El Machete C2 activity. 
The URLs are crafted to resemble legitimate government or media documents in 
Spanish but deliver compressed archives containing malware. Filenames disguised 
as official reports and judicial notices reflect classic social engineering. 
Executable names such as GoogleUpdate.exe and Chrome.exe attempt to hide 
malicious tooling behind familiar software names. The large number of MD5 and 
SHA256 hashes confirms payload reuse across multiple campaigns.


---

## Threat Actor Profile: El Machete

### Identity and Background

| Attribute | Details |
|---|---|
| Also Known As | APT-C-43, G0095 |
| Origin | Latin America (assessed) |
| Active Since | 2010 |
| MITRE ATT&CK ID | G0095 |
| Motivation | Cyber espionage / strategic intelligence collection |
| Technical Sophistication | Low to Medium, but persistent and adaptive |

El Machete is a Spanish-speaking cyber espionage group assessed to be based 
in Latin America. Attribution remains analytically assessed rather than formally 
confirmed, but linguistic evidence, victimology, and operational patterns strongly 
suggest origins in South or Central America.

The group has been active since at least 2010 and has remained operational even 
after several public disclosures by major security researchers. Public reporting 
in 2014, 2017, 2019, and the early 2020s shows continued adaptation rather than 
disappearance, consistent with a persistent intelligence collection actor.

---

### Motivation

El Machete is primarily motivated by cyber espionage and long-term intelligence 
gathering. The group repeatedly targets military, governmental, diplomatic, and 
strategic organizations rather than focusing on financially motivated crimes such 
as ransomware or direct bank fraud.

Stolen data includes government correspondence, military documents, navigation 
routes, geolocation data, browser credentials, and surveillance material — 
strongly supporting an espionage mission rather than simple monetization.

---

### Known Targets and Campaigns

**Primary Target Sectors:**
- Government and military institutions
- Intelligence services and embassies
- Law enforcement bodies
- Telecommunications providers
- Energy organizations

**Geographic Focus:**
- Venezuela, Ecuador, Colombia, Peru, Cuba, Argentina, Bolivia, Nicaragua
- Additional victims reported in the US, Russia, Spain, Germany, UK, and Asia

**Notable Campaigns:**
| Campaign | Year | Description |
|---|---|---|
| Machete Campaign | 2014 | Large-scale attacks on Latin American military and government targets |
| Sharpening the Machete | 2019 | Mass exfiltration from Venezuelan institutions |
| Russia-Ukraine Lures | 2022+ | Geopolitically themed decoy documents used as phishing lures |

---

### Tools and Malware

**Core Malware:** Machete (also known as Pyark / Fpyark)
- Python-based modular spyware
- Packaged into Windows executables using PyInstaller

**Documented Capabilities:**
- Keylogging
- Screen capture
- Audio and video recording
- Browser credential theft
- Clipboard monitoring
- File enumeration and exfiltration
- Geolocation tracking

**Abused Legitimate Tools:**
- msiexec.exe
- wscript.exe
- certutil.exe
- Scheduled Tasks

---

## MITRE ATT&CK Mapping

The observed activity aligns with known El Machete behaviour across multiple 
MITRE ATT&CK tactics.

| Tactic | Technique IDs | Observed Behaviour |
|---|---|---|
| Initial Access | T1566.001, T1566.002 | Spear-phishing attachments and links carrying malicious files |
| Execution | T1059.006, T1218.007 | Python scripts, macros, batch files, and msiexec-based execution |
| Persistence | T1053.005, T1547.001 | Scheduled tasks and Registry/Startup persistence |
| Defense Evasion | T1036.005, T1027 | Masquerading as legitimate software and code obfuscation |
| Collection | T1056.001, T1113, T1125 | Keylogging, screenshots, webcam/audio capture, browser data theft |
| Command & Control | T1071.001, T1071.002 | HTTP and FTP communication to attacker infrastructure |
| Exfiltration | T1041, T1052.001 | Data exfiltration over C2 channels and physical media |

---

### IOC-to-TTP Mapping

| Observed IOC Pattern | Likely Technique | Analytical Meaning |
|---|---|---|
| .scr files disguised as documents | Spear-phishing attachment | Victims tricked into running malware appearing as a legitimate document |
| Archive download URLs (.rar/.zip) | Malware delivery / user execution | Payloads staged behind convincing lures |
| GoogleUpdate.exe / Chrome.exe | Masquerading | Malware disguised as trusted software to reduce suspicion |
| %AppData% directories and scheduled tasks | Persistence | Malicious components survive reboots and maintain access |
| Destination IPs and rotating domains | Command and Control | Infrastructure supports remote control and exfiltration |
| Repeated hashes across events | Payload reuse | Campaigns linked through shared malware families |


---

## Threat Assessment

| Assessment Metric | Value |
|---|---|
| Threat Type | Advanced Persistent Threat / Cyber Espionage |
| Primary Motivation | Strategic intelligence collection |
| Technical Sophistication | Low to medium, but persistent and adaptive |
| Primary Victim Sectors | Government, military, diplomatic, telecoms, energy |
| Overall Risk Level | **Medium to High** |

El Machete should be considered a medium-to-high threat to organizations that 
manage politically, diplomatically, militarily, or strategically valuable 
information. The group does not rely on the most advanced zero-day techniques; 
however, its effectiveness comes from persistence, believable lures, tailored 
targeting, and the ability to remain quiet inside victim environments for long 
periods.

The risk is heightened by the group's focus on espionage rather than immediate 
disruption. In many cases, espionage actors aim to stay undetected while stealing 
information continuously, meaning the business impact may include data leakage, 
intelligence loss, reputational harm, and long-term exposure.

---

## Detection and Mitigation Recommendations

### Email Security
- Strengthen email security controls to detect and quarantine suspicious archive 
files, executable attachments, and socially engineered messages using government 
or military-themed document names

### Network Controls
- Block or closely monitor known malicious IPs, domains, and hostnames identified 
in this report
- Use network detection logic to identify unexpected FTP over port 21, repeated 
connections to dynamic DNS domains, and unusual data uploads from high-value endpoints

### Endpoint Detection
- Monitor for execution of unusual .scr files and suspicious archive extraction 
followed by executable launch
- Monitor processes impersonating legitimate software names such as Chrome, 
GoogleUpdate, Java, or Python
- Review scheduled tasks, startup entries, and %AppData% execution paths for 
persistence indicators
- Deploy EDR capabilities with behavioural rules for keylogging, screenshot 
capture, browser credential theft, and suspicious outbound FTP or HTTP activity

### User Awareness
- Provide training focused on highly targeted spear-phishing and document-lure 
attacks, especially for staff in sensitive government or administrative roles

### Threat Intelligence
- Maintain a threat intelligence process that continuously ingests new IOCs into 
monitoring workflows and revisits historical logs when new indicators become available

---

## Conclusion

This project demonstrated the practical value of MISP for structured threat 
intelligence work. The platform was successfully deployed, feeds were configured, 
events were queried, and indicator data was extracted in a way that supports both 
technical detection and threat actor profiling.

The evidence reviewed aligns strongly with El Machete, a persistent espionage 
actor that combines social engineering, malware masquerading, and long-term data 
collection against strategic targets. The collected indicators and their correlation 
across events provide a usable basis for monitoring, hunting, and defensive hardening.

---

## References

- MITRE ATT&CK Group G0095 — https://attack.mitre.org/groups/G0095/
- ESET Research: Machete just got sharper (2019)
- CIRCL: OSINT Sharpening the Machete (2019)
- CIRCL: El Machete Malware Attacks Cut Through LATAM (2017)
- FireHOL Blocklist — https://github.com/firehol/blocklist-ipsets
- MISP Project — https://www.misp-project.org
