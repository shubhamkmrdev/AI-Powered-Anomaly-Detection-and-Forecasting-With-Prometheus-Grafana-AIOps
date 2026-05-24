# Step 1: Deploy the Monitoring Stack

## 🚀 Start Your AIOps Environment.

In this step, you will deploy a complete monitoring environment using Docker Compose.  
The stack includes:

- **Prometheus** for metrics collection and storage
- **Grafana** for visualization and dashboards
- Docker networking and persistent storage volumes

---

# 📁 Project Structure

```text
project-root/
├── docker-compose.yml
├── prometheus/
│   └── prometheus.yml
└── grafana/
    └── provisioning/
        └── datasources/
            └── datasource.yml
```

---

# 🐳 Docker Compose Configuration

Create a file named `docker-compose.yml` in your project root directory.

## 📄 docker-compose.yml

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
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--web.console.libraries=/usr/share/prometheus/console_libraries'
      - '--web.console.templates=/usr/share/prometheus/consoles'
      - '--web.enable-lifecycle'
    restart: unless-stopped
    networks:
      - aiops-net

  grafana:
    image: grafana/grafana:latest
    container_name: aiops-grafana
    ports:
      - "3000:3000"
    environment:
      - GF_SECURITY_ADMIN_USER=admin
      - GF_SECURITY_ADMIN_PASSWORD=Admin@123!
      - GF_USERS_ALLOW_SIGN_UP=false
    volumes:
      - ./grafana/provisioning:/etc/grafana/provisioning
      - grafana-storage:/var/lib/grafana
    depends_on:
      - prometheus
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

# 📊 Configure Grafana Datasource

Create the datasource configuration directory:

```bash
mkdir -p grafana/provisioning/datasources
```

---

## 📄 grafana/provisioning/datasources/datasource.yml

```yaml
# Grafana Datasource Configuration
# Automatically provisions Prometheus as a datasource

apiVersion: 1

datasources:
  - name: Prometheus
    type: prometheus
    access: proxy
    url: http://prometheus:9090
    isDefault: true
    editable: true
    jsonData:
      httpMethod: POST
      timeInterval: 15s
    version: 1
```

---

# ⚙️ Configure Prometheus

Create the Prometheus configuration directory:

```bash
mkdir -p prometheus
```

---

## 📄 prometheus/prometheus.yml

```yaml
# Prometheus Configuration for AIOps Lab

global:
  scrape_interval: 15s
  evaluation_interval: 15s

  external_labels:
    monitor: 'aiops-monitoring'
    environment: 'learning'

scrape_configs:

  # Monitor Prometheus
  - job_name: 'prometheus-monitor'
    static_configs:
      - targets: ['localhost:9090']
        labels:
          service: 'prometheus'
          team: 'platform'

  # Monitor Grafana
  - job_name: 'grafana'
    static_configs:
      - targets: ['grafana:3000']
        labels:
          service: 'grafana'
          team: 'platform'
```

---

# 🔧 What Happens During Deployment

When Docker Compose starts the stack:

- Docker downloads required images
- Prometheus container starts on port `9090`
- Grafana container starts on port `3000`
- Persistent storage volumes are attached
- Internal bridge networking is configured
- Grafana automatically connects to Prometheus

> ⏱️ Estimated startup time: **30–60 seconds**

---

# ▶️ Launch the Stack

Run the following command from the project directory:

```bash
docker compose up -d
```
<img width="583" height="193" alt="image" src="https://github.com/user-attachments/assets/dacf264a-5be8-4eab-a629-b820dc742c9a" />

> 💡 The `-d` flag runs the containers in detached mode.

---

# ✅ Verify Running Containers

Check container status:

```bash
docker compose ps
```
<img width="1111" height="127" alt="image" src="https://github.com/user-attachments/assets/f3d53bcd-6008-46bf-a109-61321399f450" />

Expected output:

- `prometheus` status should be `Up`
- `grafana` status should be `Up`

---

# Step 2: Verify Prometheus

## 📊 Access the Prometheus UI

Prometheus provides a web interface for querying and exploring metrics.

---

## 📍 Access Details

| Setting | Value |
|---|---|
| Port | `9090` |
| Authentication | Disabled |
| URL | `http://localhost:9090` |

---

# 🩺 Check Health Status

```bash
curl -s http://localhost:9090/-/healthy
```

Expected output:

```text
Prometheus is Healthy.
```

---

# 🌐 Open Prometheus in Browser

1. Open the browser terminal
2. [Enter](http://YourIP:9090/)
3. Click

You should now see the Prometheus query interface.
<img width="1365" height="291" alt="image" src="https://github.com/user-attachments/assets/16f3226e-3ce8-443d-96dd-199fd0f22305" />

---

# 🔍 Optional Connectivity Check

```bash
curl -I http://localhost:9090
```

---

# Step 3: Verify Grafana

## 📈 Access the Grafana Dashboard

Grafana provides dashboards and visual analytics for metrics stored in Prometheus.

---

## 📍 Login Credentials

| Setting | Value |
|---|---|
| Port | `3000` |
| Username | `admin` |
| Password | `Admin@123!` |

---

# 🩺 Check Grafana Health

```bash
curl -s http://localhost:3000/api/health
```

Expected response:

```json
{
  "database": "ok"
}
```

---

# 🌐 Open Grafana in Browser

1. Open the browser terminal
2. [Enter](http://192.168.56.143:3000/)
3. Click

The Grafana login page should appear.
<img width="1362" height="299" alt="image" src="https://github.com/user-attachments/assets/6af0a667-434a-4bfe-98ce-277b95c61dbf" />

---

# 🎉 Deployment Successful

## ✅ Monitoring Stack Ready

You now have a fully operational monitoring environment consisting of:

| Component | Purpose | Port |
|---|---|---|
| Prometheus | Metrics collection | `9090` |
| Grafana | Visualization dashboards | `3000` |
| Docker Compose | Service orchestration | N/A |

---

# 📚 Key Concepts

## Monitoring Workflow

```text
Metrics Collection → Analysis → Visualization
```
## Achieved in this stage
- Prometheus collects metrics
- Grafana visualizes monitoring data
- Docker Compose manages infrastructure services

---

# 🎓 Next Steps

In the next stages,  will learn:

- Configure additional scrape targets
- Deploy Node Exporter
- Create custom PromQL queries
- Build anomaly detection pipelines
- Implement AI-based forecasting

---

# 🌟 Environment Ready for AIOps

monitoring foundation is now active and prepared for advanced observability and AI-driven operations workflows.
