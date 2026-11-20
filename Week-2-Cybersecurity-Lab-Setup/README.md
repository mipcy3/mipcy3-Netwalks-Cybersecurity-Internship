# PENETRATION TESTING REPORT

## Footprinting & Network Scanning Phases
*W2-PM-FINAL | CYBERSECURITY | NETWORKWALKS*

| Field | Detail |
| :--- | :--- |
| *Pentester Name (Cybersecurity Professional)* | David Ayomipo Gege |
| *Program / Batch* | B084 - Networkwalks Cybersecurity Internship |
| *Date* | 12 November 2026 |
| *Modules Completed* | W2-PM1 (Footprinting with Multiple Kali Tools)<br>W2-PM5 (Zenmap Network Scanning) |
| *Client / Target* | networkwalks.com & Tester's Local Area Network (10.0.0.0/24) |
| *Permission* | Written Letter of Authorization (NW-LOA-B082-017) signed by Sonia John, Managing Director, Networkwalks |
| *Phases Covered* | Phase 1: Footprinting & Reconnaissance<br>Phase 2: Network Scanning & Host Discovery |

---

## 1. Liability Disclaimer
This penetration testing and reconnaissance assessment was conducted strictly under written authorization (*Reference No. NW-LOA-B082-017, issued by **Sonia John, Managing Director, Networkwalks) for educational and internship training purposes under **Batch B084*. All activities were strictly limited to non-destructive passive/light reconnaissance of networkwalks.com and host discovery scanning of the tester's own local subnet (10.0.0.0/24), in full compliance with all scope boundaries and prohibited-activity conditions.

---

## 2. Introduction
This report documents the reconnaissance and network scanning activities performed during Week 2. The objective was to gather publicly available domain intelligence, identify web technologies and defensive controls protecting `networkwalks.com`, and enumerate active hosts and network topology within the local `10.0.0.0/24` subnet using Kali Linux and Zenmap.

---

## 3. Tools Used

| Tool | Purpose in Assessment | Module |
| :--- | :--- | :--- |
| **`whois`** | Domain registration, registrar details, and authoritative name server lookup | W2-PM1 |
| **`whatweb`** | Web server, CMS, plugin, and frontend technology fingerprinting | W2-PM1 |
| **`nslookup`** | Forward DNS resolution to identify the target IPv4 address | W2-PM1 |
| **`curl -I`** | HTTP response header inspection and security header analysis | W2-PM1 |
| **`wafw00f`** | Web Application Firewall (WAF) detection and vendor identification | W2-PM1 |
| **`dnsrecon`** | Comprehensive DNS record enumeration (`SOA`, `NS`, `A`, `MX`, `TXT`/`SPF`) | W2-PM1 |
| **`ip -4 addr`** | Local IPv4 interface and CIDR subnet mask discovery | W2-PM5 |
| **`Zenmap (Nmap GUI)`** | ICMP/ARP Ping Sweep (`nmap -sn`), MAC address discovery, and visual network topology mapping | W2-PM5 |

---

## 4. Activities Performed

### 4.1 Footprinting & Reconnaissance (`W2-PM1` — Target: `networkwalks.com`)
* **Domain Registration (`whois`):** Identified GoDaddy.com, LLC as the domain registrar, authoritative name servers (`://domaincontrol.com`), and confirmed DNSSEC is currently `unsigned`.
* **Technology Fingerprinting (`whatweb`):** Identified host IP `192.232.216.135` (United States) running an `Apache` web server behind `x-nginx-cache`, powered by `WordPress 7.1.4` and `WordPress Download Manager 3.3.76`, with administrative contact `info@networkwalks.com`.
* **DNS Resolution (`nslookup`):** Queried resolver `8.8.8.8` and confirmed `networkwalks.com` resolves to `192.232.216.135`.
* **HTTP Header Analysis (`curl -I`):** Confirmed `HTTP/1.1 200 OK` status, `X-nginx-cache: WordPress`, `X-Endurance-Cache-Level: 0`, `Referrer-Policy: no-referrer-when-downgrade`, and exposed `/wp-json/` REST API link headers.
* **WAF Detection (`wafw00f`):** Verified that `https://networkwalks.com` is actively protected by **ModSecurity (SpiderLabs) WAF**.
* **DNS Enumeration (`dnsrecon`):** Enumerated full zone records including `SOA`, `NS`, `A`, `MX` mail servers, and `TXT`/`SPF` email validation records.

### 4.2 Network Scanning & Host Discovery (`W2-PM5` — Target: `10.0.0.0/24`)
* **Local IP & Subnet Identification:** Executed `ip -4 addr` on interface `eth0`, identifying host IP `10.0.0.2/24` and defining the local LAN scan range as **`10.0.0.0/24`**.
* **Host Discovery Ping Scan (`nmap -sn 10.0.0.0/24`):** Performed an ICMP/ARP ping sweep across all 256 addresses in the subnet using Zenmap with root privileges (`sudo zenmap`).
* **Discovered Live Hosts & MAC Addresses:**
  * **Total Live Hosts:** `2` active hosts discovered in the post-reboot scan (`10.0.0.1` and `10.0.0.2`) *(3 hosts including `10.0.0.3` prior to reboot)*.
  * **`10.0.0.1` (Virtual Gateway):** Up (`0.00051s` latency) — MAC Address: `52:55:0A:00:00:01`
  * **`10.0.0.2` (Local Kali Linux Host):** Up (`localhost`)
* **Network Topology Mapping:** Generated the visual network topology graph with the **Topology Legend** enabled and exported the topology map in PDF format (`zenmap-topology.pdf`).

---

## 5. Risk Analysis / Impact

| Finding | Severity | Potential Impact |
| :--- | :--- | :--- |
| **Exposed CMS & Plugin Versions (`WordPress 7.1.4`, `WPDM 3.3.76`)** | Medium | Attackers can fingerprint exact software versions and search for known CVEs targeting outdated plugins or core versions. |
| **Missing HTTP Security Headers (`HSTS`, `X-Frame-Options`, `CSP`)** | Low | Increases exposure to clickjacking or protocol downgrade attacks if users connect over unencrypted HTTP. |
| **Unsigned DNSSEC (`DNSSEC: unsigned`)** | Low | Leaves domain DNS responses without cryptographic authentication against DNS spoofing or cache poisoning. |
| **Publicly Enumerable DNS & Email Metadata (`MX`, `TXT`, `info@`)** | Low | Facilitates targeted phishing or spam campaigns against exposed organizational email addresses. |
| **ModSecurity WAF Active Protection** | Informational | Positive security control; actively filters common web application attacks such as SQLi and XSS. |

---

## 6. Recommendations
* **Harden HTTP Response Headers:** Configure Apache/Nginx to enforce `Strict-Transport-Security` (HSTS), `X-Frame-Options: SAMEORIGIN`, `X-Content-Type-Options: nosniff`, and a restrictive `Content-Security-Policy` (CSP).
* **Suppress Version Banners:** Disable version disclosure in WordPress meta tags and HTTP headers to reduce passive fingerprinting accuracy.
* **Enable DNSSEC:** Configure DNSSEC signing through the domain registrar (GoDaddy) to protect DNS integrity.
* **Network Segmentation & Firewalling:** Restrict unnecessary ICMP/ARP responses on internal network segments and maintain regular patch management for all discovered LAN hosts.

---

## 7. Conclusion
Both passive/active reconnaissance (`W2-PM1`) and local network scanning (`W2-PM5`) were executed successfully. While `networkwalks.com` benefits from strong perimeter protection via **ModSecurity WAF**, tightening HTTP security headers, enabling DNSSEC, and minimizing software version banners will further reduce its external attack surface.

---

## 8. Evidences Collected

### Module 1 (`W2-PM1`): Footprinting `networkwalks.com`
* **Task 1 — `whois` Domain Lookup:**
  ![Whois Part 1]

<img width="1600" height="865" alt="01-whois-a" src="https://github.com/user-attachments/assets/9fd884b7-18b6-4235-b019-537e65a8b668" />

  ![Whois Part 2]
  <img width="1600" height="900" alt="01-whois-b" src="https://github.com/user-attachments/assets/8f74b21d-5db2-453e-a225-5bdd4ddaa31d" />

  ![Whois Part 3] <img width="1600" height="900" alt="01-whois-c" src="https://github.com/user-attachments/assets/6d5dbecc-a5f3-439a-9bf7-e3e4ddd865fa" />
<img width="1600" height="900" alt="01-whois-d" src="https://github.com/user-attachments/assets/45e4c66a-e9cf-4fa5-bf8b-7f877251d305" />

* **Task 2 — `whatweb` Technology Fingerprinting:**
  <img width="1598" height="310" alt="02-whatweb-a" src="https://github.com/user-attachments/assets/38e434cc-5976-4263-ae50-e11c3d979769" />

* **Task 3 — `nslookup` DNS Resolution:**
 <img width="578" height="391" alt="03-nslookup" src="https://github.com/user-attachments/assets/530bcba2-2b33-4be0-8ee3-ea958d6d5814" />

* **Task 4 — `curl -I` HTTP Headers:**
<img width="1595" height="591" alt="04-netwalks" src="https://github.com/user-attachments/assets/fc040e8e-94d5-4990-afab-710f26b9f68a" />

* **Task 5 — `wafw00f` Firewall Detection:**
 <img width="761" height="569" alt="05-wafw00f" src="https://github.com/user-attachments/assets/ac09e57a-634a-4492-b459-48b9301064e4" />

* **Task 6 — `dnsrecon` DNS Enumeration:**
<img width="1268" height="646" alt="06-dnsrecon" src="https://github.com/user-attachments/assets/5a94f1a7-55c9-4b3f-9b01-6a28851881b4" />


### Module 5 (`W2-PM5`): Zenmap Network Scanning
* **Task 2 — Local IP & Subnet (`ip -4 addr`):**
 <img width="944" height="359" alt="07-ip-subnet" src="https://github.com/user-attachments/assets/22323280-519d-465a-9d51-aa3d501ec02b" />

* **Tasks 3–6 — Zenmap Ping Scan (`nmap -sn 10.0.0.0/24`):**
<img width="671" height="611" alt="08-zenmap-ping-scan" src="https://github.com/user-attachments/assets/4e9e681a-ecdc-4658-8291-cd5385d8a9ca" />

* **Task 7 — Zenmap Network Topology & Legend:**
 <img width="981" height="678" alt="09-zenmap-topology" src="https://github.com/user-attachments/assets/75fb7c85-aef1-4fb8-9b0f-6333485fc28f" />

