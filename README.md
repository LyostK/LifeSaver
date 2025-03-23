# LifeSaver

The **LifeSaver** project aims to improve patient management and orientation within hospitals by providing a real-time and historical tracking solution. Its goal is to reduce the overload of emergency services by offering a clear and visual representation of patient routing.

---

## Table of Contents

- [Project Context](#project-context)
- [System Architecture](#system-architecture)
  - [Overview](#overview)
  - [Server Details](#server-details)
- [Technical Requirements](#technical-requirements)
- [Technology Choices Justification](#technology-choices-justification)
- [Constraints and Expected Performance](#constraints-and-expected-performance)
- [Data Qualification, Sizing, and Security](#data-qualification-sizing-and-security)

---

## Project Context

Hospitals face an overload due to inefficient patient routing by emergency services (SAMU). This issue often stems from a lack of accessible, real-time, or historical data regarding patient orientations. LifeSaver provides clear and visual tracking of patient flows, segmented by time and facility, which helps alleviate this overload.

---

## System Architecture

The solution is based on a distributed architecture with three distinct servers, each reflecting a real-world role within the project:

### Overview

1. **Data Acquisition:**  
   The Hospital Server acts as the data source, providing updated medical data every hour.

2. **Automation:**  
   The ISEN Server automatically triggers a Bash script every minute to synchronize files via RClone.

3. **Parsing and Processing:**  
   Data is processed and converted into an indexable format using Logstash (within a Docker container). Wazuh is used for security monitoring.

4. **Storage and Visualization:**  
   Processed data is stored in Elasticsearch and made available for analysis and visualization via Kibana.

<img width="1403" alt="Capture d’écran 2025-03-23 à 13 21 23" src="https://github.com/user-attachments/assets/91bab412-ee99-4d29-8cbb-d89e2e9092b5" />

   

### Server Details

- **Hospital Server (Emitter):**  
  - **Role:** Source of medical data  
  - **System:** Virtual machine running Rocky Linux on Proxmox  
  - **Formats:** CSV, JSON  
  - **Security:** According to the hospital server’s security policies

- **ISEN Server (Processing):**  
  - **Role:** Parsing and transforming data  
  - **Technologies:**  
    - Logstash running in a Docker container for parsing  
    - Wazuh for security monitoring  
    - Bash script for file synchronization via RClone  
    - Crontab to automate the script execution (every minute)  
  - **Data Formats:** Converts CSV/JSON into a structured JSON format for Elasticsearch  
  - **Security:** Isolated container and restricted access via VPN

- **Airbus Server (Storage & Visualization):**  
  - **Role:** Storing and visualizing data  
  - **Technologies:**  
    - Elasticsearch for data indexing  
    - Kibana for data visualization  
  - **Security:** Access restricted by VPN

---

## Technical Requirements

- **Data Sources:**  
  Files in CSV and JSON format are generated and updated hourly by the hospital.

- **Data Processing:**  
  - Logstash is used for parsing and transforming data.  
  - Wazuh provides security monitoring.  
  - A Bash script synchronizes files automatically via a crontab.

- **Storage and Visualization:**  
  - Data is indexed in Elasticsearch.  
  - Kibana is used for visualization and analysis.

---

## Technology Choices Justification

The chosen technologies were selected to meet the specific needs of the project:

- **Proxmox & Rocky Linux:**  
  Provide flexible virtualization and a secure, robust platform for hosting virtual machines.

- **Logstash & Docker:**  
  Ensure efficient normalization and transformation of data while offering isolation and portability.

- **Elasticsearch & Kibana:**  
  Offer a complete solution for data indexing and visualization, making real-time analysis easier.

- **RClone & Crontab:**  
  Ensure fast and automated synchronization of files between servers.

- **Wazuh:**  
  Monitors security and detects potential intrusions.

- **VPN (Wireguard):**  
  Ensures encryption and secure communication between servers.

---

## Constraints and Expected Performance

- **Technical Constraints:**  
  - The system must handle variable data throughput (multiple files concurrently).  
  - It should be resilient in case one of the servers fails.

- **Security:**  
  - Controlled access with authentication for Kibana.  
  - Continuous monitoring via Wazuh along with logging of access and requests.  
  - Regular backups and a recovery plan in case of system failure.

---

## Data Qualification, Sizing, and Security

- **Data Qualification:**  
  - Integrity and format of data are verified with every import.  
  - Logs are maintained in Logstash to track processing and detect anomalies.  
  - Filters are applied in Logstash to exclude invalid data entries.

- **Data Sizing:**  
  - Elasticsearch indexing is optimized with specific mappings.  
  - Index rotation (daily, weekly) is applied if data volume increases significantly.

- **Data Security:**  
  - Access to Kibana is restricted to authorized users only.  
  - TLS/SSL is enabled to secure communications between Logstash, Elasticsearch, and Kibana.  
  - An audit log tracks all access and modifications.

---

For any questions or feedback, please open an issue or contact us.
