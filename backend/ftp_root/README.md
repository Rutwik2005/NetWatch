# 🛡 NetWatch: Real-Time Network Monitoring, Intrusion Detection & FTP Activity Analysis

A complete full-stack system for real-time network traffic monitoring, rule-based intrusion detection, FTP server activity analysis, and a live React dashboard.

---

## 📁 Project Structure

```
netwatch/
├── backend/
│   ├── main.py              # FastAPI server (API endpoints)
│   ├── sniffer.py            # Scapy packet capture
│   ├── detector.py           # Rule-based intrusion detection
│   ├── ftp_server.py         # Custom FTP server (pyftpdlib)
│   ├── ftp_monitor.py        # FTP activity anomaly detection
│   ├── model.py              # Optional ML anomaly detection
│   ├── requirements.txt      # Python dependencies
│   └── utils/
│       └── helpers.py        # Utility functions
│
├── frontend/
│   ├── package.json
│   ├── vite.config.js
│   ├── tailwind.config.js
│   ├── index.html
│   ├── public/
│   └── src/
│       ├── components/
│       │   ├── StatsCards.jsx       # Dashboard stat cards
│       │   ├── TrafficChart.jsx     # Live traffic line chart
│       │   ├── AlertsPanel.jsx      # Alert notifications
│       │   ├── PacketTable.jsx      # Packet log table
│       │   ├── TopIPs.jsx           # Top IPs bar + protocol pie chart
│       │   └── FTPPanel.jsx         # FTP activity panel
│       ├── pages/
│       │   └── Dashboard.jsx        # Main dashboard page
│       ├── services/
│       │   └── api.js               # Axios API service
│       ├── App.jsx
│       ├── main.jsx
│       └── index.css
│
└── README.md
```

---

## 🔧 How It Works

### Data Flow

```
Sniffer (Scapy) → Backend API → Detection Engine → In-Memory Store → API → Frontend Dashboard
FTP Server       → FTP Monitor → Backend API     → Alerts          → API → Frontend Dashboard
```

1. **Sniffer** captures live network packets and sends them to the backend via HTTP POST
2. **Backend** receives packets, runs rule-based detection and optional ML anomaly scoring
3. **Detection Engine** checks for DDoS, port scans, and traffic spikes
4. **FTP Server** logs all connections, logins, uploads, and downloads
5. **FTP Monitor** analyzes FTP activity for suspicious patterns
6. **Frontend** polls the backend every 5 seconds and displays data in the dashboard

### Detection Rules

| Rule | Trigger | Severity |
|------|---------|----------|
| DDoS | >100 requests/IP in 10s | High |
| Port Scan | >15 unique ports in 5s | Medium |
| Traffic Spike | >200 packets/sec | High |
| FTP Brute Force | ≥5 failed logins in 60s | High |
| Suspicious Upload | >10 uploads/IP in 60s | Medium |
| Large File Transfer | File >50 MB | Medium |

---

## 🚀 Setup & Run Instructions

### Prerequisites

- **Python** 3.10+ with pip
- **Node.js** 18+ with npm
- **Npcap** (Windows) or **libpcap** (Linux/Mac) — required for Scapy

### Step 1: Backend Setup

```bash
cd netwatch/backend

# Create a virtual environment (recommended)
python -m venv venv
venv\Scripts\activate     # Windows
# source venv/bin/activate  # Linux/Mac

# Install dependencies
pip install -r requirements.txt
```

### Step 2: Start the Backend API

```bash
cd netwatch/backend
python main.py
```

The API will start at `http://localhost:8000`.

### Step 3: Start the FTP Server (separate terminal)

```bash
cd netwatch/backend
python ftp_server.py
```

FTP server starts on port **2121** with these accounts:

| Username | Password | Access |
|----------|----------|--------|
| admin | admin123 | Full read/write |
| user1 | password1 | Full read/write |
| guest | guest | Read-only |
| anonymous | — | Read-only |

### Step 4: Start the Packet Sniffer (separate terminal, requires admin)

```bash
# Run as Administrator / sudo
cd netwatch/backend
python sniffer.py
```

> ⚠️ **The sniffer requires admin/root privileges** to capture network packets.

### Step 5: Start the Frontend

```bash
cd netwatch/frontend
npm install    # if not done already
npm run dev
```

Open **http://localhost:5173** in your browser.

---

## 📊 Dashboard Components

| Component | Description |
|-----------|-------------|
| **Stats Cards** | Total packets, alerts, active IPs, FTP activity |
| **Traffic Chart** | Real-time area chart of packet flow |
| **Alerts Panel** | Color-coded alerts with severity badges |
| **Top IPs** | Bar chart of most active source IPs |
| **Protocol Distribution** | Donut chart of TCP/UDP/ICMP distribution |
| **FTP Panel** | Upload/download logs, login activity, FTP alerts |
| **Packet Table** | Filterable table of captured packets |

---

## 🔌 API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/packet` | Receive packet data from sniffer |
| POST | `/ftp-event` | Receive FTP event for monitoring |
| GET | `/packets?limit=50` | Get recent packets |
| GET | `/alerts?limit=50` | Get all alerts |
| GET | `/ftp-activity?limit=50` | Get FTP activity logs |
| GET | `/stats` | Get dashboard summary stats |
| GET | `/traffic-data?limit=60` | Get traffic timeline |
| GET | `/top-ips?limit=10` | Get top source IPs |
| GET | `/protocols` | Get protocol distribution |
| GET | `/health` | Health check |

---

## 🧪 Testing the FTP Server

Connect using any FTP client:

```bash
# Using command-line ftp
ftp localhost 2121

# Or use FileZilla / WinSCP
# Host: localhost, Port: 2121, Username: admin, Password: admin123
```

---

## 🤖 Machine Learning (Optional)

The system includes an optional Isolation Forest anomaly detector. It requires `scikit-learn` and `joblib` (included in requirements.txt). The model:

- Auto-trains on synthetic "normal" traffic data on first startup
- Saves/loads from `anomaly_model.pkl`
- Adds anomaly scores to packet analysis

If ML dependencies are not installed, the system runs with rule-based detection only.

---

## ⚙ Technology Stack

| Layer | Technology |
|-------|-----------|
| Backend API | FastAPI + Uvicorn |
| Packet Capture | Scapy |
| FTP Server | pyftpdlib |
| Detection | Rule-based + Isolation Forest |
| Frontend | React + Vite |
| Styling | Tailwind CSS |
| Charts | Recharts |
| HTTP Client | Axios |

---

## 📝 Notes

- All data is stored **in-memory** (resets on server restart)
- Frontend polls every **5 seconds** (configurable in `Dashboard.jsx`)
- The sniffer batches packets (10 at a time) for efficiency
- CORS is enabled for all origins in development
- For production, configure proper CORS and authentication
