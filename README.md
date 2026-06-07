# Enterprise Intrusion Detection & Threat Monitoring System (IDS)

## 📌 Objective
This project serves as a comprehensive network security lab designed to establish total network visibility, centralize log management, and configure baseline threat detection. Serving as my Master's Project at Teesside University, the environment focuses on SIEM engineering—integrating an open-source firewall, an IDS engine, and a customized ELK pipeline to normalize and index network telemetry.

📄 **Deep Dive:** *For a comprehensive breakdown of the academic research, log normalization processes, and infrastructure setup, please review the `IDS REPORT.pdf` attached in this repository.*

## 🗺️ Network Architecture & Topology

> 📸 **Visual Proof 1: Network Topology**
> *[Insert your flowchart/Visio diagram here showing Internet -> pfSense -> Ubuntu Server -> Suricata -> ELK Stack]*
> <img width="256" height="384" alt="image" src="https://github.com/user-attachments/assets/29ac3fe9-4009-475b-aa0f-b7545d820a29" />


The architecture is built on a segmented virtual network:
1. **Perimeter Defense:** A pfSense virtual firewall configured with strict LAN/WAN routing, NAT port forwarding, and baseline firewall rules.
2. **Internal Infrastructure:** An Ubuntu Server hosting an Apache2 web application.
3. **Traffic Inspection:** Suricata deployed to continuously monitor internal network traffic and perform packet inspection against baseline rule sets.
4. **Centralized SIEM:** Filebeat extracts Suricata's `eve.json` logs and Apache2 logs, forwarding them through Logstash for parsing before indexing them into Elasticsearch.

## 🛠️ Technologies & Tools Utilized
* **Firewall/Routing:** pfSense
* **Intrusion Detection System (IDS):** Suricata
* **SIEM & Log Management:** ELK Stack (Elasticsearch, Logstash, Kibana), Filebeat
* **Infrastructure:** Ubuntu Server, Linux Command Line (CLI), Apache2

## 💻 Implementation & Configuration Proof

### 1. pfSense Firewall Configuration
* Deployed pfSense to manage internal and external network traffic boundaries.
* Configured custom NAT rules to allow controlled access to the internal Apache2 web server while dropping unauthorized external traffic.

> 📸 **Visual Proof 2: Firewall & Routing**
> *[Insert screenshot of the pfSense Web GUI showing your Firewall Rules or NAT Port Forwarding configuration]*
<img width="640" height="300" alt="image" src="https://github.com/user-attachments/assets/708f35e4-7efa-4447-92fe-3e7a6e585e71" />
<img width="613" height="161" alt="image" src="https://github.com/user-attachments/assets/12420934-110f-49a9-8a8e-7e71ed240968" />
<img width="733" height="274" alt="image" src="https://github.com/user-attachments/assets/3b2d0baa-1c7c-47f6-83c4-232937831789" />
<img width="713" height="276" alt="image" src="https://github.com/user-attachments/assets/1e2713dc-f78c-45ef-9f28-07ff0adb0115" />


### 2. Suricata IDS Deployment
* Installed and configured Suricata to monitor the internal network interface.
* Implemented standard threat detection rule sets to monitor active network traffic and log anomalies.

### 3. ELK Stack SIEM Pipeline & Log Normalization
* **Filebeat:** Configured as a lightweight shipper to ingest unstructured Suricata threat logs.
* **Logstash:** Engineered a custom data pipeline utilizing Grok filters to parse raw logs into normalized, searchable fields.

> 📸 **Visual Proof 3: Log Normalization Pipeline**
> *[Insert terminal/code screenshot showing the JSON output of eve.json, your Logstash Grok filter, or Filebeat actively shipping logs]*
<img width="811" height="887" alt="image" src="https://github.com/user-attachments/assets/a0fb23e5-acde-44c7-afb1-847936fdc3a2" />


## 📊 Centralized Security Dashboard

* **Elasticsearch & Kibana:** Indexed the parsed logs to build interactive dashboards, establishing a baseline for network traffic visualization and reducing manual log review time.

> 📸 **Visual Proof 4: The Kibana Dashboard**
> *[Insert your most impressive Kibana screenshot here, showing pie charts, traffic graphs, or the Discover tab with parsed logs]*

## 🎯 Key Takeaways
* **SIEM Engineering:** Gained hands-on experience building a centralized logging pipeline, ensuring multi-source data is accurately parsed and indexed.
* **Infrastructure Deployment:** Successfully architected a multi-layered network defense utilizing the exact tools deployed in enterprise environments.
* **Network Visibility:** Transformed raw, noisy system data into a clean, monitored, and highly visual dashboard framework.
