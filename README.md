# AI-Based Intrusion Detection System (IDS)

An educational Final Year Project (FYP) implementation of an AI-based IDS that analyzes flow-level metadata (packet size, timing, duration) without decrypting payloads. It supports training classic ML models and provides a simple Flask dashboard to upload traffic flows and view anomaly detections and statistics.

## Features
- Flow-level feature extraction from packet or flow CSVs
- Training scripts for RandomForest (optional XGBoost)
- Inference utilities with supervised model or unsupervised fallback (IsolationForest)
- Flask dashboard: upload CSV, view detections, traffic stats, and a results table
- Kafka consumer stub for near-real-time integration

## Project Structure
```
src/
  features/flow_features.py       # Flow feature extraction utilities
  models/train.py                 # Train RandomForest/XGBoost
  models/infer.py                 # Load model and predict anomalies
  webapp/app.py                   # Flask dashboard
  webapp/templates/index.html     # Dashboard UI
  webapp/static/css/style.css     # Basic styling
  webapp/static/js/app.js         # (optional) UI scripts
  streaming/kafka_consumer.py     # Kafka consumer stub
  data/preprocess_cicids.py       # CICIDS preprocessing stub
  config/settings.yaml            # Basic config
artifacts/                        # Saved models (created at runtime)
```

## Prerequisites
- Python 3.9+ recommended
- Optional: Wireshark/Tshark for packet capture (if using `pyshark` later)
- Optional: Kafka for real-time streaming (`kafka-python` client included)

## Setup
```powershell
# From the project root (c:\FYP)
python -m venv .venv
.\.venv\Scripts\python -m pip install --upgrade pip
.\.venv\Scripts\python -m pip install -r requirements.txt
```

## Data Format
- Packet-level CSV expected columns (minimal): `timestamp, src_ip, dst_ip, src_port, dst_port, protocol, packet_size`
- Flow-level CSV expected columns: `packet_count, byte_count, mean_packet_size, std_packet_size, flow_duration, mean_iat, std_iat` (and optional identifiers)

The feature extractor will aggregate packet-level CSVs into flow-level features when possible.

## Train a Model
Prepare a labeled flow CSV with a `label` column (0=benign, 1=attack):
```powershell
.\.venv\Scripts\python src\models\train.py --input data\flows.csv --label label --algo rf --save-path artifacts\model.joblib
```

Notes:
- RandomForest (`rf`) requires no additional setup.
- XGBoost (`xgb`) is optional; install `xgboost` first: `.\.venv\Scripts\python -m pip install xgboost`.
- Metrics (Precision, Recall, F1) are printed after training.

## Run the Flask Dashboard
```powershell
.\.venv\Scripts\python src\webapp\app.py
```
Then open `http://localhost:5000/`. Upload a CSV of flows to see predicted anomalies and summary statistics.

## Real-time (Kafka) – Optional
- Configure a Kafka broker and topic, then adapt `src/streaming/kafka_consumer.py` to consume flow JSON messages.
- The web app can be extended to ingest streaming results via websockets or server-sent events.

## Notes and Limitations
- Payload inspection is out of scope due to encryption; detections rely on metadata patterns.
- False positives are possible on highly variable traffic.
- Real-time performance on high-speed networks may require optimization.

## License
This project is intended for academic use in a final year project context.

## CICIDS2017 Integration
- Download the dataset from `https://www.unb.ca/cic/datasets/ids-2017.html` or Kaggle and place the CSVs under `c:\FYP\dataset\CICIDS2017\`.

- Preprocess to the project’s flow feature schema:
  ```powershell
  .\.venv\Scripts\python src\data\preprocess_cicids.py --input dataset\CICIDS2017 --output data\cicids_flows.csv
  ```
  - Produces `packet_count, byte_count, mean_packet_size, std_packet_size, flow_duration, mean_iat, std_iat, label, attack_category`.
  - `label` is `0` for BENIGN and `1` for attacks; `attack_category` maps common CICIDS labels (DDoS, DoS, Port Scan, Brute Force, Botnet, Web Attack, etc.).

- Train a model on the preprocessed flows:
  ```powershell
  .\.venv\Scripts\python src\models\train.py --input data\cicids_flows.csv --label label --algo rf --save-path artifacts\model.joblib
  ```

- Configure the dashboard to use the saved model by setting in `src\config\settings.yaml`:
  ```yaml
  model_path: artifacts/model.joblib
  ```

- Use in the dashboard:
  - Upload `data\cicids_flows.csv` via the Upload CSV form, or copy it to `data\dataflow.csv` and press `Replay dataflow.csv`.
  - New visuals: Attack Breakdown (pie), Attack Frequency Over Time (line), Top Sources by Attack Volume (table), and an Attack Filter in the top bar.
  - For live capture (optional), set your `tshark_path` and `default_interface` in `src\config\settings.yaml`, then click `Start Live Capture`.