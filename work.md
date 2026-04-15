# NetWatch Architecture & Data Flow

### 🏗️ 1. Project Architecture Overview
Your project is an Intrusion Detection System (IDS) divided into a **Python FastAPI Backend** and a **React + Vite Frontend**.

*   **The Sniffer (`backend/sniffer.py`)**: Uses external tools like Scapy to continuously capture live network packets (TCP, UDP, ICMP, etc.) traversing your network interfaces.
*   **The Engine (`backend/detector.py`, `model.py`, `ftp_monitor.py`)**: As packets are captured, they are analyzed. You have rule-based logic to detect common attacks (like DDoS, Port Scans, UDP Floods) and an Isolation Forest Machine Learning model (`anomaly_model.pkl`) to identify unknown erratic behavior. It also actively monitors a custom FTP server for suspicious activity.
*   **The Core App (`backend/main.py`)**: This is the central FastAPI server running on `localhost:8000`. It receives the captured packets, stores them in-memory, maintains counters (e.g., active IPs, protocol counts, traffic timeline), and exposes this data via RESTful API endpoints.
*   **The Command Center (Frontend)**: This is your React dashboard. It visually interprets the security metrics, alerts, and system health by continuously fetching JSON data from the FastAPI backend.

### 🔌 2. Where the Frontend Gets Its Data
The React frontend requests its data **exclusively** from the backend API running at `http://localhost:8000`. The logic for fetching this data is centralized in `frontend/src/services/api.js` using the **Axios** HTTP client.

Here is the exact mapping of what the frontend shows and where it gets it from:

*   **Dashboard Stats (Total Packets, Alerts, Active IPs)**: Fetched from backend `GET /stats`. The frontend uses these numbers to populate the high-level metric cards.
*   **Traffic Timeline (The real-time chart)**: Fetched from backend `GET /traffic-data`. The backend buckets packet counts per second and sends an array of timestamps and counts to render the graph.
*   **Top Source IPs**: Fetched from backend `GET /top-ips`. The backend returns a list of the IP addresses that have sent the most packets, along with their geographical location (injected via `utils.geo`).
*   **Protocol Distribution (Pie Chart)**: Fetched from backend `GET /protocols`. The backend simply tallies how many packets used TCP, UDP, ICMP, etc.
*   **Live Packets Table**: Fetched from backend `GET /packets`. Displays the most recently intercepted raw network traffic.
*   **Alerts Log (The threat list)**: Fetched from backend `GET /alerts`. Displays a combined list of triggered network anomalies and FTP violations (e.g., failed logins, large transfers).
*   **FTP Activity**: Fetched from backend `GET /ftp-activity`. Brings in logs for server uploads, downloads, and connections.

### ⚡ 3. The Full Data Flow Lifecycle
1.  A packet arrives on your computer.
2.  `sniffer.py` captures it and sends a `POST /packet` request to the FastAPI server (`main.py`).
3.  `main.py` parses the packet, updates its internal counters, and passes it to `detector.py` and `model.py` to see if it's an attack.
4.  If it is an attack, an alert is generated and stored in memory. *If the severity is high/medium, it also triggers a Windows firewall block command (`utils/blocker.py`).*
5.  Meanwhile, the React frontend is making `GET` requests (via `axios` in `api.js`) every few seconds or on page load.
6.  The FastAPI server responds to these `GET` requests with the latest in-memory JSON data.
7.  The React components (using tools like Recharts and Tailwind) re-render to display the new information.

No persistent database (like PostgreSQL or MongoDB) is currently used; everything relies on the high-performance **in-memory** data structures declared in `main.py` (like `packets_store`, `ip_counter`, `total_packets_received`).
