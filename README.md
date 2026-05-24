# 🚀 AI-Powered Anomaly Detection and Forecasting with Prometheus, Grafana & AIOps

## 📖 Overview  
This project demonstrates how **Artificial Intelligence transforms IT Operations (AIOps)** by building a complete observability and intelligence pipeline—from raw system metrics to AI-powered anomaly detection and forecasting.

You will progressively build a real-world monitoring and AI system using industry-standard tools:

- 🐳 Docker & Docker Compose  
- 🔍 Prometheus  
- 📊 Grafana  
- 🤖 Python (Machine Learning with scikit-learn)

---

## 💡 Core Idea

> **AI is only as good as the data it learns from.**

This lab series follows the **AIOps Pyramid**:

- 🧠 AI-Driven Insights (Anomaly Detection & Forecasting)  
- 📊 High-Quality Data (Metrics, Logs, Traces)

Everything starts with **high-quality metrics data**, collected using Prometheus.

---

# 🧭 Project Structure

This project is divided into **5 progressive exercises**:

---

## 📊 Exercise 1: AI in AIOps – Data to Decisions (Foundation Setup)

### 🎯 Objective
Build the foundational monitoring stack and understand AIOps concepts.

### 🧩 What You Learn
- What is AIOps and why it matters  
- AIOps Pyramid (Data → Insights → Actions)  
- Metrics-first monitoring approach  
- Basics of Prometheus & Grafana  
- Containerized infrastructure using Docker Compose  

### 🛠️ What You Do
- Deploy Prometheus + Grafana using Docker Compose  
- Access dashboards:
  - Prometheus → http://localhost:9090  
  - Grafana → http://localhost:3000  
- Understand how metrics are collected and visualized  

---

## 📊 Exercise 2: Collecting the Data Fuel (Prometheus Deep Dive)

### 🎯 Objective
Learn how Prometheus collects real-time system metrics.

### 🧩 What You Learn
- Prometheus pull-based architecture  
- Metrics exposition format (`/metrics`)  
- Node Exporter for system metrics  
- Scrape jobs and configuration (`prometheus.yml`)  
- Time-series data fundamentals  

### 🛠️ What You Do
- Configure Prometheus scrape targets  
- Add Node Exporter monitoring  
- Inspect raw metrics using `curl`  
- Validate targets in Prometheus UI  
- Understand counters, gauges, and histograms  

---

## 📊 Exercise 3: Basic Analysis with PromQL

### 🎯 Objective
Transform raw metrics into meaningful insights using PromQL.

### 🧩 What You Learn
- PromQL syntax and query model  
- Label filtering and time-series selection  
- Rate calculations for counters  
- Aggregations (sum, avg, max, min)  
- CPU usage and system-level analytics  
- Limitations of static threshold monitoring  

### 🛠️ What You Do
- Write PromQL queries  
- Calculate CPU usage from raw metrics  
- Build aggregations across instances  
- Create alert-style queries  
- Understand why static thresholds fail in modern systems  

---

## 🤖 Exercise 4: AI-Powered Anomaly Detection

### 🎯 Objective
Introduce Machine Learning into monitoring systems.

### 🧩 What You Learn
- Why static thresholds are insufficient  
- Machine learning-based anomaly detection concepts  
- Isolation Forest algorithm (unsupervised learning)  
- Feature engineering for time-series data  
- ML vs traditional monitoring  

### 🛠️ What You Do
- Train ML model using Prometheus data  
- Build feature pipeline (rolling stats, trends, time features)  
- Run real-time anomaly detection  
- Compare ML vs threshold-based alerts  

### 🧠 Output
- Trained anomaly detection model  
- Real-time anomaly scoring system  
- ML-powered monitoring pipeline  

---

## 🔮 Exercise 5: AI-Driven Forecasting (Coming Soon)

### 🎯 Objective
Predict system behavior using time-series forecasting.

### 🧩 What You Will Learn
- Time-series forecasting (Prophet / ML models)  
- Capacity planning  
- Predictive scaling  
- Proactive incident prevention  

---

# 🧰 Tech Stack

| Tool | Purpose |
|------|--------|
| 🐳 Docker | Containerization |
| 🔍 Prometheus | Metrics collection & storage |
| 📊 Grafana | Visualization & dashboards |
| 🖥️ Node Exporter | System metrics |
| 🤖 Python | ML and automation |
| 📦 pandas / numpy | Data processing |
| 🧠 scikit-learn | Machine learning models |

---

# 🏗️ Architecture Overview

## 🔹 System Flow

```text
Applications
   ↓
Prometheus (Metrics Collection - Port 9090)
   ↓
Time-Series Database (TSDB)
   ↓
Grafana (Visualization - Port 3000)
   ↓
Python ML Engine (Anomaly Detection & Forecasting)
