# 🛡️ NetWatch: Project Overview & Significance

## 🌟 Introduction
**NetWatch** is a comprehensive, real-time Network Intrusion Detection System (IDS) and monitoring platform. It is designed to bridge the gap between complex network traffic and actionable security insights through a modern, responsive dashboard.

---

## 🏗️ System Architecture
The project is built on a distributed 4-tier architecture:
1.  **The Sentinel (Packet Sniffer)**: Uses **Scapy** to intercept every packet (TCP, UDP, ICMP) in real-time.
2.  **The Guard (IDS Engine)**: Analyzes traffic patterns against rule-based signatures and **Machine Learning (Isolation Forest)** models to flag anomalies.
3.  **The Core (FastAPI Backend)**: Orchestrates data flow, manages in-memory state, and provides a secure REST API for the frontend.
4.  **The Command Center (React Frontend)**: A premium, theme-aware dashboard that visualizes traffic metrics, alerts, and server activity using **Tailwind CSS** and **Recharts**.

---

## 🛡️ Key Features & Significance

### 1. Real-Time Intrusion Detection
NetWatch provides immediate visibility into common network attacks:
*   **DDoS Detection**: Monitors for high-volume request spikes from single sources.
*   **Port Scan Analysis**: Identifies attackers probing for open vulnerabilities across multiple ports.
*   **UDP Flood Protection**: Specifically monitors connectionless traffic for potential resource exhaustion attacks.

### 2. Deep FTP Activity Monitoring
Unlike standard monitors, NetWatch includes a custom-built FTP server with deep protocol inspection:
*   **Brute Force Prevention**: Tracks failed login attempts to stop unauthorized access.
*   **Data Leakage Alerts**: Flags suspicious or unusually large file transfers.
*   **Full Audit Trail**: Every upload, download, and connection is logged and visualized.

### 3. Machine Learning Anomaly Detection
Beyond static rules, the system includes an **Isolation Forest** model. This allows the system to detect "unknown-unknowns"—traffic that doesn't trigger any specific rule but deviates significantly from the baseline "normal" behavior of the network.

---

## 🌍 Real-World Impact & Applications

### 🔒 Affordable Security for SMEs
For small and medium enterprises (SMEs), expensive enterprise security suites (like Snort or Cisco) are often out of reach. NetWatch offers a lightweight, open-source alternative for proactively monitoring their servers without high costs.

### 🎓 Educational & Training Tool
In cybersecurity education, seeing an attack in real-time is far more effective than reading about it. NetWatch serves as a "Learning Lab" where students can trigger attacks (using the provided simulation tools) and observe exactly how they appear on a professional dashboard.

### 🛠️ IT Troubleshooting & Health
NetWatch isn't just for security; it's a powerful diagnostic tool. By visualizing **Protocol Distribution** and **Top Source IPs**, network administrators can quickly identify bandwidth hogs, hardware failures, or routing loops.

---

## 📊 Technical Rules & Thresholds

| Threat Type | Trigger Logic | Severity |
| :--- | :--- | :--- |
| **DDoS** | >100 requests from one IP in 10s | 🔴 High |
| **UDP Flood** | >50 UDP packets from one IP in 5s | 🔴 High |
| **Port Scan** | >15 unique ports accessed in 5s | 🟡 Medium |
| **Traffic Spike** | >200 packets/sec overall | 🔴 High |
| **Brute Force** | ≥5 failed FTP logins in 60s | 🔴 High |
| **Large Transfer** | File transfer >50 MB | 🟡 Medium |
| **New Discovery** | First-time IP connection detected | 🟢 Low |

---

## ⚙️ Technology Stack
*   **Backend**: Python, FastAPI, Scapy, Uvicorn
*   **Database**: In-memory (High performance, focus on live state)
*   **Machine Learning**: Scikit-learn (Isolation Forest), NumPy
*   **Frontend**: React, Vite, Tailwind CSS, Recharts
*   **Networking**: FTP (pyftpdlib), HTTP (Axios)

---
*Developed for real-time security visibility and proactive network defense.*
