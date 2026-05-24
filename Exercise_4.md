## Exercise 4: Building AI-Powered Anomaly Detection with Grafana

---

# 📘 Exercise Overview

In this project exercise, I implemented an AI-powered anomaly detection workflow using Prometheus, Grafana, and Python machine learning libraries. Instead of relying only on static monitoring thresholds, I trained an `IsolationForest` model to automatically learn normal CPU behavior and detect unusual system activity in real time.

This project exercise demonstrates how AI can improve observability by reducing alert fatigue and identifying abnormal infrastructure behavior proactively.

---

# 📚 AIOps Learning Journey

```text
     ┌───────────────────────────────┐
     │  3. AI-Driven Insights        │  ← Current Focus
     │     (Anomaly Detection)       │
     ├───────────────────────────────┤
     │  2. Setup Node-exporter       |
     |   for High-Quality Data       │
     ├───────────────────────────────┤
     │  1. set up Monitoring stack   │
     └───────────────────────────────┘
```

This project builds on previous monitoring and PromQL concepts by adding machine learning–based anomaly detection.

---

# 🚀 Why AI-Based Monitoring Matters

Traditional monitoring systems often depend on static thresholds such as:

```text
Alert if CPU > 80%
```

This approach creates several challenges:

- Frequent false alerts during normal workload spikes
- Missed anomalies below fixed thresholds
- Continuous manual tuning
- High alert fatigue

Machine learning improves this process by:

- Learning normal system behavior automatically
- Detecting statistical deviations instead of fixed limits
- Adapting to changing workloads
- Reducing unnecessary alerts

---

# 🛠️ Step 1: Prepare Python Environment and Install ML Libraries

## 📘 Objective

Set up a Python virtual environment and install all dependencies required for anomaly detection.

---

## Update Package Repository

```bash
apt update
```

---

## Install Python venv and pip

```bash
apt install -y python3.12-venv python3-pip
```

---

## Create Project Virtual Environment

```bash
cd /root/scripts
python3 -m venv venv
```

---

## Activate Virtual Environment

```bash
source /root/scripts/venv/bin/activate
```

---

## Upgrade pip

```bash
pip install --upgrade pip
```

---

## Install Required Packages

```bash
pip install -r /root/scripts/requirements.txt
```

---

## requirements.txt

```text
scikit-learn==1.3.2
pandas==2.1.3
numpy==1.26.2
prometheus-api-client==0.5.3
```

---

## Verify Installation

```bash
python3 -c "import sklearn, pandas, numpy, prometheus_api_client; print('All packages installed successfully')"
```

---

# 📦 Libraries Used

| Package | Purpose |
|---|---|
| scikit-learn | IsolationForest anomaly detection model |
| pandas | Time-series data manipulation |
| numpy | Numerical computations |
| prometheus-api-client | Query Prometheus metrics using Python |

---

# 📊 Step 2: Access Grafana Dashboard

## 📘 Objective

Verify that the monitoring stack is operational before training the ML model.

---

## Access Grafana

Open Grafana on:

```text
http://localhost:3000
```

Login credentials:

```text
Username: admin
Password: GrafanaRocks123!
```

---

## Dashboard Verification

Open the pre-configured dashboard:

```text
Node Health Monitor
```

The dashboard contains:

- CPU Usage
- Memory Available
- Disk Usage
- Network Traffic

---

# 📈 Dashboard Metrics

## CPU Usage

```promql
100 - (avg(rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)
```

---

## Memory Available

```promql
node_memory_MemAvailable_bytes / 1024 / 1024 / 1024
```

---

## Disk Usage

```promql
(1 - node_filesystem_avail_bytes{fstype=~"ext4|xfs|btrfs"} / node_filesystem_size_bytes{fstype=~"ext4|xfs|btrfs"}) * 100
```

---

## Network Traffic

### RX

```promql
rate(node_network_receive_bytes_total[5m]) / 1024 / 1024
```

### TX

```promql
rate(node_network_transmit_bytes_total[5m]) / 1024 / 1024
```

---

# 🎯 Step 3: Train IsolationForest ML Model

## 📘 Objective

Train an anomaly detection model using historical CPU metrics collected by Prometheus.

---

## Run Training Script

```bash
python3 /root/scripts/train_anomaly_model.py
```

---

# 🧠 What the Training Script Does

The script performs the following operations:

- Connects to Prometheus
- Fetches historical CPU metrics
- Creates engineered ML features
- Trains an IsolationForest model
- Saves the trained model locally

---

# 📊 Feature Engineering

The model uses engineered features to improve anomaly detection accuracy.

## Features Created

| Feature | Purpose |
|---|---|
| rolling_mean | Smooth CPU behavior |
| rolling_std | Detect variability |
| rate_of_change | Detect spikes |
| hour | Learn time-based patterns |

---

# ⚙️ IsolationForest Configuration

```python
IsolationForest(
    contamination=0.1,
    random_state=42,
    n_estimators=100
)
```

---

# 📁 Model Output

The trained model is stored at:

```text
/root/anomaly_model.pkl
```

---

# 💡 Training Insights

The model learns:

- Typical CPU ranges
- Time-of-day behavior
- CPU volatility patterns
- Expected rate changes

---

# 🎯 Step 4: Run Real-Time Anomaly Detection

## 📘 Objective

Use the trained model to analyze live metrics and detect abnormal behavior.

---

## Execute Detection Script

```bash
python3 /root/scripts/detect_anomalies.py
```

---

# 🔍 Detection Workflow

The detection script performs the following tasks:

1. Loads the trained ML model
2. Queries recent CPU metrics from Prometheus
3. Applies feature engineering
4. Predicts anomalies
5. Displays anomaly analysis

---

# 📊 Detection Output

The script classifies data as:

| Prediction | Meaning |
|---|---|
| 1 | Normal |
| -1 | Anomaly |

---

# 📈 Example Detection Summary

```text
Total samples: 60
Normal: 54
Anomalies: 6
```

---

# ⚡ Continuous Monitoring

The script also supports continuous anomaly monitoring.

Example:

```text
Check every 30 seconds
Analyze last 10 minutes of metrics
```

---

# 🧠 Understanding Anomaly Scores

Lower anomaly scores indicate more unusual behavior.

The script identifies:

- Sudden CPU spikes
- High volatility
- Unexpected usage patterns
- Time-based abnormalities

---

# ⚖️ ML Monitoring vs Static Thresholds

| Feature | Static Thresholds | ML Anomaly Detection |
|---|---|---|
| Configuration | Manual | Automatic |
| Adaptation | Static | Dynamic |
| Context Awareness | No | Yes |
| False Positives | High | Low |
| Detects Subtle Issues | Limited | Strong |

---

# ✅ Where ML Performs Better

## Scenario 1: Daily CPU Spikes

Static monitoring generates alerts daily.

ML learns this pattern and ignores expected behavior.

---

## Scenario 2: Slow Resource Degradation

Static threshold:

```text
No alert until threshold breached
```

ML detects unusual gradual increases much earlier.

---

## Scenario 3: Overnight Activity

Static threshold may ignore unusual nighttime CPU spikes.

ML identifies these as abnormal based on historical behavior.

---

# 📌 Best Monitoring Strategy

A hybrid monitoring approach works best:

- ML anomaly detection for dynamic metrics
- Static thresholds for critical hard limits
- Combined alerting for stronger reliability

---

# 🎓 Key Concepts Learned

## IsolationForest

IsolationForest isolates anomalies using random partitioning.

Outliers are easier to isolate than normal data points.

---

## Feature Engineering

Raw metrics alone are insufficient.

Feature engineering improves anomaly detection quality significantly.

---

## Proactive Monitoring

AI enables proactive monitoring by identifying unusual behavior before outages occur.

---

# 📈 Real-World Benefits

AI-driven monitoring can provide:

- Faster issue detection
- Reduced false alerts
- Lower downtime
- Improved operational efficiency

---

# 🚀 Project Outcomes

By completing this project, I successfully:

- Built a Prometheus + Grafana monitoring environment
- Trained an IsolationForest ML model
- Implemented feature engineering for time-series metrics
- Performed real-time anomaly detection
- Compared ML monitoring with traditional threshold-based systems
- Learned proactive observability concepts

---

# 🌟 Final Result

This project demonstrates how machine learning can transform traditional monitoring into intelligent observability by automatically detecting anomalies, adapting to changing workloads, and improving operational awareness in modern infrastructure environments.
