# 🔐 Firewall & Monitoring System for a Web Server

> **MSc Cybersecurity Dissertation Project** — Teesside University (CIS4055)  
> *An integrated open-source network security system combining a firewall, intrusion detection, and real-time monitoring.*

---

## 📌 Overview

This project implements a **multi-layered network security system** that protects a web server using entirely open-source tools. It integrates:

- **pfSense** — Next-generation firewall
- **Suricata** — Intrusion Detection System (IDS)
- **ELK Stack** (Elasticsearch, Logstash, Kibana) — Log management & visualization
- **Filebeat** — Lightweight log shipper
- **Apache2 on Ubuntu Server** — Web application hosting

The system provides real-time threat detection, centralized log management, and visual dashboards — making it suitable for small-to-medium enterprises or educational environments.

---

## 🏗️ System Architecture

```
Internet
   │
   ▼
 Router  (Virgin Media Hub 3.0 — Port Forwarding)
   │
   ▼
Firewall (pfSense 2.7.0 — VM on VirtualBox)
   │
   ▼
Internal Network (192.168.1.0/24)
   │
   ▼
Ubuntu Server 24.04 LTS (192.168.1.102)
   ├── Apache2        → Web Application (theanugrah.com)
   ├── Suricata IDS   → Network Traffic Monitoring → eve.json
   ├── Filebeat       → Ships Suricata logs → Elasticsearch
   ├── Logstash       → Processes Apache2 access/error logs → Elasticsearch
   ├── Elasticsearch  → Indexes & stores all logs (port 9200)
   └── Kibana         → Visualization Dashboard (port 5601)
```

---

## 🛠️ Tech Stack

| Component | Tool | Version |
|-----------|------|---------|
| Firewall | pfSense | 2.7.0 |
| OS | Ubuntu Server | 24.04 LTS |
| Web Server | Apache2 | Latest |
| IDS | Suricata | 7.0.3 |
| Log Shipper | Filebeat | 7.x |
| Log Pipeline | Logstash | 7.x |
| Search Engine | Elasticsearch | 7.x |
| Dashboard | Kibana | 7.x |
| Virtualisation | Oracle VirtualBox | Latest |

---

## ⚙️ Setup & Installation

### Prerequisites

- Oracle VirtualBox installed on host machine
- pfSense ISO: [https://www.pfsense.org](https://www.pfsense.org)
- Ubuntu Server ISO: [https://ubuntu.com](https://ubuntu.com)

---

### Step 1 — VirtualBox Network Setup

Create two virtual networks in VirtualBox:

| Network Type | Name | IP Prefix |
|---|---|---|
| Host-only | VirtualBox Host-Only Ethernet Adapter | 192.168.1.100/24 |
| NAT Network | WAN-1 | 111.111.111.112/28 |

---

### Step 2 — pfSense Firewall VM

```
RAM: 4096 MB | CPU: 2 cores | Storage: 16 GB
Adapter 1: Bridged (WAN)
Adapter 2: Host-only (LAN)
```

After boot, configure LAN/WAN interfaces and access the web interface at `192.168.1.1`  
Default credentials: `admin` / `pfsense` (change immediately)

**Firewall Rules:**
- Block private networks (RFC 1918)
- Block bogon networks
- NAT Port Forward: WAN:80 → 192.168.1.102:80 (Ubuntu web server)

---

### Step 3 — Ubuntu Server VM

```
RAM: 4096 MB | Storage: 25 GB
Adapter: Host-only (Internal Network)
Username: webserver | Password: webserver
IP Address: 192.168.1.102
```

**Initial Setup:**
```bash
sudo apt update
ip a   # Verify IP is 192.168.1.102
```

---

### Step 4 — Apache2 Web Server

```bash
sudo apt install apache2 -y
sudo systemctl start apache2
sudo systemctl enable apache2
```

**Web App Directory:** `/var/www/webapp/`  
**Config File:** `/etc/apache2/sites-enabled/test.project.final.conf`

```bash
# Disable default site and enable your own
sudo a2dissite 000-default.conf
sudo a2ensite test.project.final.conf
sudo systemctl reload apache2
```

**UFW Firewall on Ubuntu:**
```bash
sudo ufw allow ssh
sudo ufw allow http
sudo ufw allow https
sudo ufw enable
```

---

### Step 5 — ELK Stack

**Install Java first:**
```bash
sudo apt install openjdk-11-jdk -y
```

**Install Elasticsearch:**
```bash
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-7.x.list
sudo apt install elasticsearch -y
sudo systemctl enable elasticsearch && sudo systemctl start elasticsearch
```

Key config in `/etc/elasticsearch/elasticsearch.yml`:
```yaml
network.host: 192.168.1.102
http.port: 9200
discovery.type: single-node
```

**Install Logstash:**
```bash
sudo apt install logstash -y
```

Config in `/etc/logstash/conf.d/webapp-log.conf` — reads Apache2 access/error logs and ships to Elasticsearch.

**Install Kibana:**
```bash
sudo apt install kibana -y
```

Key config in `/etc/kibana/kibana.yml`:
```yaml
server.port: 5601
server.host: "192.168.1.102"
elasticsearch.hosts: ["http://192.168.1.102:9200"]
```

Access Kibana at: `http://192.168.1.102:5601`

---

### Step 6 — Suricata IDS

```bash
sudo add-apt-repository ppa:oisf/suricata-stable
sudo apt-get install suricata -y
sudo systemctl enable suricata && sudo systemctl start suricata
```

Key config in `/etc/suricata/suricata.yaml`:
```yaml
af-packet:
  - interface: enp0s3
```

Logs saved to: `/var/log/suricata/eve.json`

---

### Step 7 — Filebeat

```bash
sudo apt-get install filebeat -y
```

Key config in `/etc/filebeat/filebeat.yml`:
```yaml
filebeat.inputs:
  - type: log
    enabled: true
    paths:
      - /var/log/suricata/eve.json

output.elasticsearch:
  hosts: ["http://192.168.1.102:9200"]

setup.kibana:
  host: "http://192.168.1.102:5601"
```

```bash
sudo filebeat setup --index-management -E output.logstash.enabled=false \
  -E 'output.elasticsearch.hosts=["http://192.168.1.102:9200"]'
sudo systemctl enable filebeat && sudo systemctl start filebeat
```

---

### Step 8 — DNS Configuration

Domain registered via **GoDaddy**: `theanugrah.com`

| Type | Name | Data |
|------|------|------|
| A | @ | Your Public IP |
| CNAME | www | theanugrah.com |

---

## 🧪 Testing & Verification

### Check All Service Statuses

```bash
sudo systemctl status apache2
sudo systemctl status elasticsearch
sudo systemctl status logstash
sudo systemctl status kibana
sudo systemctl status suricata
sudo systemctl status filebeat
```

### Verify Log Collection

```bash
# Apache2 access logs
cat /var/www/webapp/logs/access.log

# Apache2 error logs
cat /var/www/webapp/logs/error.log

# Suricata network logs
sudo nano /var/log/suricata/eve.json

# Filebeat logs
sudo less /var/log/filebeat/filebeat.log
```

### Kibana Index Patterns

1. Go to **Kibana → Management → Index Patterns**
2. Create pattern: `webapp-logs-*` (Apache2 logs via Logstash)
3. Create pattern: `filebeat-*` (Suricata logs via Filebeat)
4. Set time field: `@timestamp`

---

## 📊 Monitoring Dashboards

| Dashboard | Index Pattern | Data Source |
|-----------|--------------|-------------|
| Web App Logs | `webapp-logs-*` | Apache2 via Logstash |
| Network Monitoring | `filebeat-*` | Suricata via Filebeat |

The Kibana interface enables filtering by IP address, error codes, attack patterns, and time range for real-time threat analysis.

---

## 📁 Project Structure

```
Project/
├── pfsense/                    # pfSense VM files
├── webserver/                  # Ubuntu Server VM files
│   ├── /var/www/webapp/        # Web application
│   │   ├── index.html
│   │   ├── about.html
│   │   ├── contact.html
│   │   ├── css/styles.css
│   │   └── logs/
│   │       ├── access.log
│   │       └── error.log
│   ├── /etc/apache2/           # Apache2 config
│   ├── /etc/elasticsearch/     # Elasticsearch config
│   ├── /etc/logstash/          # Logstash pipeline config
│   ├── /etc/kibana/            # Kibana config
│   ├── /etc/suricata/          # Suricata IDS config
│   └── /etc/filebeat/          # Filebeat config
└── README.md
```

---

## ⚠️ Known Limitations

1. **DNS Propagation Delay** — Updates to DNS records take time; frequent server restarts worsen availability.
2. **Integration Complexity** — Multiple tools require careful configuration alignment and technical expertise.
3. **No Firewall Log Monitoring** — pfSense logs are not yet piped into ELK (planned for future).

---

## 🔮 Future Work

- **Firewall Log Monitoring** — Pipe pfSense logs into Kibana for complete perimeter visibility
- **ML/AI Integration** — Anomaly detection algorithms for advanced threat identification
- **SOAR Automation** — Auto-respond to detected threats in real-time
- **Cloud Integration** — Extend monitoring to AWS Security Hub / Azure Security Centre

---

## 👨‍💻 Author

**Anugrah Kizhakke Veedu**  
MSc Cybersecurity — Teesside University  
Module: CIS4055 Computing Masters Project  
Supervisor: Harry Stewart

---

## 📜 License

This project was developed as part of an academic dissertation at Teesside University. All tools used are open-source.

---

## 📚 Key References

- pfSense: https://www.pfsense.org
- Ubuntu Server: https://ubuntu.com
- Elastic Stack: https://www.elastic.co
- Suricata IDS: https://suricata.io
- Oracle VirtualBox: https://www.virtualbox.org

