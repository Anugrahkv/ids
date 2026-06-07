# Enterprise Intrusion Detection & Threat Monitoring System (IDS)

## 📌 Objective
This project serves as a comprehensive network security lab designed to establish total network visibility, centralize log management, and configure baseline threat detection. Serving as my Master's Project at Teesside University, the environment focuses on SIEM engineering—integrating an open-source firewall, an IDS engine, and a customized ELK pipeline to normalize and index network telemetry.

## 🗺️ Network Architecture & Topology
> **Note:** *[Insert a screenshot of your network diagram here using Draw.io or Lucidchart showing the flow from the WAN -> pfSense -> Internal Network (Ubuntu/Apache2) & Suricata -> ELK Stack]*

The architecture is built on a segmented virtual network:
1. **Perimeter Defense:** A pfSense virtual firewall configured with strict LAN/WAN routing, NAT port forwarding, and baseline firewall rules.
2. **Internal Infrastructure:** An Ubuntu Server hosting an Apache2 web application.
3. **Traffic Inspection:** Suricata deployed to continuously monitor internal network traffic and perform packet inspection against baseline rule sets.
4. **Centralized SIEM:** Filebeat extracts Suricata's `eve.json` logs and Apache2 logs, forwarding them through Logstash for parsing before indexing them into Elasticsearch and visualizing the data in Kibana.

## 🛠️ Technologies & Tools Utilized
* **Firewall/Routing:** pfSense
* **Intrusion Detection System (IDS):** Suricata
* **SIEM & Log Management:** ELK Stack (Elasticsearch, Logstash, Kibana), Filebeat
* **Infrastructure:** Ubuntu Server, Linux Command Line (CLI), Apache2

## 💻 Step-by-Step Implementation

### 1. pfSense Firewall Configuration
* Deployed pfSense to manage internal and external network traffic boundaries.
* Configured custom NAT rules to allow controlled access to the internal Apache2 web server while dropping unauthorized external traffic.

### 2. Suricata IDS Deployment & Configuration
* Installed and configured Suricata to monitor the internal network interface.
* Implemented standard threat detection rule sets to monitor active network traffic and log anomalies.

### 3. ELK Stack SIEM Pipeline & Log Normalization
* **Filebeat:** Configured as a lightweight shipper to ingest unstructured Suricata threat logs and Apache access/error logs.
* **Logstash:** Engineered a custom data pipeline utilizing Grok filters to parse raw logs into normalized, searchable fields.
* **Elasticsearch & Kibana:** Indexed the parsed logs to build interactive dashboards, establishing a baseline for network traffic visualization.

## 📊 Network Visibility & Dashboards (Visual Proof)

### Log Normalization & Parsing Pipeline
*Demonstrating the successful extraction and parsing of raw Suricata logs into structured JSON data.*
> *[Insert Screenshot of your Logstash configuration or the parsed log output in the terminal/Kibana]*

### Centralized Security Dashboard
*A macro-view of network telemetry, active traffic origins, and system baseline data.*
> *[Insert Screenshot of your custom Kibana Dashboard showing network traffic]*

### Firewall & Routing Configuration
*Visual proof of the strict access control lists (ACLs) and NAT routing.*
> *[Insert Screenshot of your pfSense firewall rules GUI]*

## 🎯 Key Takeaways
* **SIEM Engineering:** Gained hands-on experience building a centralized logging pipeline, ensuring multi-source data is accurately parsed and indexed.
* **Infrastructure Deployment:** Successfully architected a multi-layered network defense utilizing the exact tools deployed in enterprise environments.
* **Network Visibility:** Transformed raw, noisy system data into a clean, monitored, and highly visual dashboard framework.
