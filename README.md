
---

```markdown
# Hybrid Intrusion Detection System (IDS)
### Signature-Based + Anomaly-Based Detection

---

## 📌 Project Overview

This project implements a **Hybrid Intrusion Detection System (IDS)** that combines:

- 🔎 **Signature-Based Detection** using Suricata  
- 📊 **Anomaly-Based Detection** using Tshark with statistical / ML-based analysis  
- 🌐 **Flask Web Dashboard** for real-time monitoring  

The system monitors network traffic, detects malicious activity, and visualizes alerts through a centralized dashboard.

---

## 🏗 System Architecture

```

```
            Network Interface
                   │
                   ▼
           ┌──────────────┐
           │  Suricata    │
           └──────────────┘
                   │
            eve.json (logs)
                   │
                   ▼
           Flask Ingest Engine
                   │
                   ▼
              Dashboard

                   ▲
                   │
           ┌──────────────┐
           │   Tshark     │
           └──────────────┘
                   │
           live_capture.py
                   │
           Anomaly Detection
                   │
                   ▼
              Dashboard
```

````
---

# 🪟 Installation Guide (Windows – Tshark Only Mode)

## 1️⃣ Install System Dependencies

```bash
sudo apt update
sudo apt install -y suricata tshark python3 python3-venv
````

Allow non-root packet capture:

```bash
sudo dpkg-reconfigure wireshark-common
sudo usermod -aG wireshark $USER
```

Log out and log back in (or reboot).

---

## 2️⃣ Setup Python Environment

```bash
git clone https://github.com/<your-username>/FYP-IDS.git
cd FYP-IDS
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

---

## 3️⃣ Start Suricata (Signature-Based Detection)

Replace `eth0` with your actual network interface:

```bash
sudo suricata -c /etc/suricata/suricata.yaml -i eth0 -l /var/log/suricata
```

Verify logs:

```bash
sudo tail -f /var/log/suricata/eve.json
```

---

## 4️⃣ Run the IDS Dashboard

```bash
source .venv/bin/activate
python src/webapp/app.py
```

Open in browser:

```
http://127.0.0.1:5000
```

---

# 🪟 Installation Guide (Windows – Tshark Only Mode)

> ⚠ Suricata is recommended on Linux.
> On Windows, use Tshark-based anomaly detection mode.

---

## 1️⃣ Install Python

```powershell
winget install Python.Python.3
```

---

## 2️⃣ Install Wireshark (Includes Tshark)

```powershell
winget install Wireshark.Wireshark
```

Confirm tshark location:

```
C:\Program Files\Wireshark\tshark.exe
```

Update `settings.yaml`:

```yaml
tshark_path: "C:\\Program Files\\Wireshark\\tshark.exe"
```

---

## 3️⃣ Setup Python Environment

```powershell
python -m venv .venv
.\.venv\Scripts\activate
pip install -r requirements.txt
```

---

## 4️⃣ Run Dashboard

```powershell
python src\webapp\app.py
```

Open:

```
http://127.0.0.1:5000
```

---

# 📦 requirements.txt

```
Flask
flask-socketio
pandas
numpy
scikit-learn
joblib
PyYAML
pyshark
scapy
requests
```

---

# 📁 .gitignore (Important)

```
.venv/
__pycache__/
*.pyc
*.log
ids.db
artifacts/*.pkl
```

Never upload:

* Suricata logs
* Virtual environments
* Database files
* Trained models (unless intended)

---

# Troubleshooting

### ❌ Tshark Not Found

Check:

```bash
which tshark
```

On Windows:

```powershell
Get-Command tshark
```

---

### ❌ Suricata Not Generating Alerts

Verify:

```bash
sudo tail -f /var/log/suricata/eve.json
```

Ensure correct interface is used.

---

### ❌ No Traffic Detected

List interfaces:

```bash
tshark -D
```

Use a valid interface like:

* `eth0`
* `wlan0`
* `enp0s3`

---

# 🎓 Academic Statement (For FYP Report)

> The proposed system integrates signature-based detection using Suricata with anomaly-based detection leveraging real-time packet capture via Tshark. The system provides a centralized monitoring dashboard implemented in Flask for real-time visualization and alert management.

---


