# Step 1: Initialize the Monitoring Stack

## 📘 Overview

In this exercise, will deploy a basic observability platform with node exporter using Docker Compose as same previous stage.

The environment includes:

- **Prometheus** for metric collection and storage
- **Node Exporter** for exposing Linux host metrics
- **Grafana** for dashboards and visualization
- Docker bridge networking and persistent storage volumes

All containers will run in detached mode and will be configured further in upcoming steps.

---

# 📁 Check Project Files

Verify that the required files and folders are available in the working directory.

```bash
ls
```

Expected output:

```text
docker-compose.yml  prometheus grafana
```

---

# 📄 Inspect the Docker Compose File

View the contents of the compose file:

```bash
cat docker-compose.yml
```

Configuration example:

```yaml
version: '3.8'

services:

  prometheus:
    image: prom/prometheus:latest
    container_name: aiops-prometheus

    ports:
      - "9090:9090"

    volumes:
      - ./prometheus:/etc/prometheus
      - prom-storage:/prometheus

    command:
      - --config.file=/etc/prometheus/prometheus.yml
      - --storage.tsdb.path=/prometheus
      - --web.console.libraries=/usr/share/prometheus/console_libraries
      - --web.console.templates=/usr/share/prometheus/consoles
      - --web.enable-lifecycle

    restart: unless-stopped

    networks:
      - aiops-net

  grafana:
    image: grafana/grafana:latest
    container_name: aiops-grafana

    ports:
      - "3000:3000"

    environment:
      GF_SECURITY_ADMIN_USER: admin
      GF_SECURITY_ADMIN_PASSWORD: GrafanaRocks123!
      GF_USERS_ALLOW_SIGN_UP: "false"

    volumes:
      - ./grafana/provisioning:/etc/grafana/provisioning
      - grafana-storage:/var/lib/grafana

    depends_on:
      - prometheus

    restart: unless-stopped

    networks:
      - aiops-net

  node-exporter:
    image: prom/node-exporter:latest
    container_name: aiops-node-exporter

    ports:
      - "9100:9100"

    command:
      - --path.rootfs=/host

    volumes:
      - /:/host:ro,rslave

    restart: unless-stopped

    networks:
      - aiops-net

networks:
  aiops-net:
    driver: bridge

volumes:
  prom-storage:
  grafana-storage:
```

---

# 🚀 Start the Containers

Run the following command from the root directory:

```bash
cd /root && docker compose up -d
```

This command downloads images if necessary and launches all services in the background.

---

# ✅ Expected Output

Docker should return output similar to:

```text
Network aiops-net created
Container aiops-prometheus started
Container aiops-grafana started
Container aiops-node-exporter started
```

---

# 🔎 Confirm Container Status

Check whether the containers are running correctly:

```bash
docker compose ps
```

Each service should display the status:

```text
Up
```

---

# 📂 Navigate to the Prometheus Directory

Move into the Prometheus configuration folder:

```bash
cd ~/prometheus
```

List the available files:

```bash
ls
```

Expected result:

```text
prometheus.yml
```

---

# 📄 Examine the Prometheus Configuration

Display the configuration file:

```bash
cat prometheus.yml
```

Starter configuration example:

```yaml
# Prometheus base configuration

global:
  scrape_interval: 15s
  evaluation_interval: 15s

  external_labels:
    monitor: 'aiops-monitoring'
    environment: 'learning'

scrape_configs:

  # Scrape jobs will be defined here
  - job_name: 'prometheus-monitor'
```

---

# Step 2: Enable Prometheus Self-Monitoring

## 📘 Overview

Prometheus can collect metrics about its own health and performance by scraping its internal metrics endpoint.

This allows administrators to monitor:

- Scrape activity
- Memory usage
- TSDB performance
- Request statistics

---

# ✏️ Modify the Configuration File

Open the Prometheus configuration using `vi`:

```bash
vi /root/prometheus/prometheus.yml
```

Add the following scrape job under `scrape_configs`:

```yaml
scrape_configs:

  - job_name: 'prometheus'

    static_configs:
      - targets:
          - 'prometheus:9090'
```

> ⚠️ YAML formatting is space-sensitive. Use spaces instead of tabs.

---

# 🔄 Reload the Service

Restart Prometheus to apply the configuration changes:

```bash
docker compose restart prometheus
```

---

# Step 3: Validate the Prometheus Target

## 📘 Overview

After restarting the service, verify that Prometheus is successfully scraping its own metrics endpoint.

---

# 🔍 Check the Metrics Endpoint

Run the following command:

```bash
curl -s http://localhost:9090/metrics | head -n 20
```

The response should contain Prometheus metric entries.

---

# 📜 Inspect Container Logs

Review recent Prometheus logs:

```bash
docker compose logs prometheus | tail -n 20
```

This helps identify configuration or startup issues.

---

# 🌐 Open the Prometheus Dashboard

Access the Prometheus UI:

1. Open the terminal menu `(⋮)`
2. Select **View Port**
3. Enter `9090`
4. Click **Open Port**

Navigate to:

```text
Status → Targets
```

---

# ✅ Expected Status

The following target should appear:

| Job | Endpoint | State |
|---|---|---|
| prometheus | http://prometheus:9090/metrics | UP |

---

# Step 4: Add Node Exporter as a Scrape Target

## 📘 Overview

Next, configure Prometheus to collect infrastructure metrics from Node Exporter.

Node Exporter exposes information such as:

- CPU statistics
- Memory usage
- Filesystem metrics
- Disk activity
- Network counters

---

# ✏️ Update prometheus.yml

Edit the configuration file again:

```bash
vi /root/prometheus/prometheus.yml
```

Add a second scrape job:

```yaml
scrape_configs:

  - job_name: 'prometheus'

    static_configs:
      - targets:
          - 'prometheus:9090'

  - job_name: 'node-exporter'

    static_configs:
      - targets:
          - 'node-exporter:9100'
```

---

# 🔄 Restart Prometheus

Apply the updated configuration:

```bash
docker compose restart prometheus
```

---

# 🔍 Verify the Configuration

Display the configuration file to confirm the Node Exporter job exists:

```bash
cat /root/prometheus/prometheus.yml
```

---

# Step 5: Confirm Node Exporter Metrics

## 📘 Overview

Verify that Prometheus is successfully scraping metrics from Node Exporter.

---

# 🔍 View Exported Metrics

Run:

```bash
curl -s http://localhost:9100/metrics | head -n 30
```

---

# 📊 Count Exported Node Metrics

Run:

```bash
curl -s http://localhost:9100/metrics | grep '^node_' | wc -l
```

The output should indicate hundreds of available metrics.

---

# 🌐 Verify Targets in the Prometheus UI

Return to:

```text
Status → Targets
```

---

# ✅ Expected Target List

| Job | Endpoint | State |
|---|---|---|
| prometheus | http://prometheus:9090/metrics | UP |
| node-exporter | http://node-exporter:9100/metrics | UP |

---

# Step 6: Query Metrics with PromQL

## 📘 Overview

Use the Prometheus expression browser to explore collected metrics using PromQL.

---

# 🌐 Open the Graph Section

In the Prometheus dashboard, select:

```text
Graph
```

---

# 🔍 Sample PromQL Queries

## 1. Check Node Exporter Availability

```promql
up{job="node-exporter"}
```

Expected output:

```text
1
```

A result of `1` confirms the exporter is reachable.

---

## 2. CPU Metrics

```promql
node_cpu_seconds_total
```

Displays CPU usage counters categorized by mode and CPU core.

---

## 3. Available Memory

```promql
node_memory_MemAvailable_bytes
```

Shows the amount of currently available system memory.

---

## 4. Network Receive Metrics

```promql
node_network_receive_bytes_total
```

Displays received network traffic per interface.

---

# 💡 Helpful Notes

- Use **Graph** view for visual analysis
- Use **Table** view for raw metric values
- Labels such as `job` and `instance` help identify targets
- Prometheus autocomplete assists with metric discovery

---

## ✅ Skills Covered

- Deployed Prometheus, Grafana, and Node Exporter
- Learned the basics of pull-based monitoring
- Configured scrape targets in Prometheus
- Verified exporter health using the Targets page
- Queried metrics with PromQL
- Built the core monitoring layer required for AIOps workflows

---

# 📚 Important Concepts

## 1. Pull-Based Collection

Prometheus periodically pulls metrics from configured endpoints instead of relying on push-based delivery.

---

## 2. Scrape Jobs

Scrape jobs define:

- Target endpoints
- Collection intervals
- Metric labels

---

## 3. Labels Improve Querying

Prometheus automatically adds labels like:

```text
job="node-exporter"
instance="node-exporter:9100"
```

These labels simplify filtering, grouping, and aggregation.

---

## 4. Exporters Expand Observability

Node Exporter is one example of many exporters available for infrastructure, databases, containers, and cloud services.

---

# 🚀 Next Stage Preview

In the next stage, will work with advanced PromQL concepts, including:

- Rate calculations
- Aggregations
- Time-series functions
- Alert rule queries
- Statistical analysis for AIOps

---

# 🌟 Monitoring Platform Ready

Prometheus-based monitoring environment is now operational and ready for advanced observability and analytics tasks.
