# In this Exercise: Performing Metric Analysis with PromQL

## 📘 Project Overview

In this project exercise, I explored how to use **PromQL (Prometheus Query Language)** to analyze infrastructure metrics collected by Prometheus.

The goal of this exercise was to understand how raw monitoring data can be transformed into meaningful operational insights using queries, filters, aggregations, and calculations.

---

# 🎯 Skills Practiced

During this exercise, I worked with the following PromQL concepts:

- Selecting metrics from Prometheus
- Filtering time-series data using labels
- Calculating rates from counter metrics
- Aggregating metrics using functions like `avg()` and `sum()`
- Measuring CPU and memory usage
- Writing threshold-based monitoring queries
- Understanding the limitations of static alerting

---

# Step 1: Querying Basic Metrics

## 📌 Objective

The first task was to verify whether monitored services were reachable using the built-in `up` metric.

The `up` metric returns:

- `1` → Target is reachable
- `0` → Target is unavailable

---

## ▶️ Query Used

```promql
up
```

---
<img width="1364" height="349" alt="image" src="https://github.com/user-attachments/assets/303657e6-92ed-4884-9ecb-0618bac27aaa" />

## ✅ Observed Output

The query returned metrics for both monitored targets:

```text
up{job="prometheus",instance="prometheus:9090"} = 1
up{job="node-exporter",instance="node-exporter:9100"} = 1
```

This confirmed that Prometheus was successfully scraping metrics from both services.

---

# Step 2: Filtering Metrics with Labels

## 📌 Objective

In this step, I filtered results using label selectors to display metrics only for the Node Exporter target.

---

## ▶️ Query Used

```promql
up{job="node-exporter"}
```

---
<img width="1360" height="302" alt="image" src="https://github.com/user-attachments/assets/06c4160c-6a53-4850-970b-423dd7aa5d11" />

## ✅ Result

The query returned only the Node Exporter time series:

```text
up{job="node-exporter",instance="node-exporter:9100"} = 1
```

This step demonstrated how labels help isolate specific metrics in Prometheus.

---

# 📚 Label Filtering Syntax

| Syntax | Description |
|---|---|
| `label="value"` | Exact label match |
| `label!="value"` | Exclude a label value |
| `label=~"regex"` | Match using regular expressions |
| `label!~"regex"` | Negative regex filtering |

---
# Step 3: Understanding Rate Calculations with `rate()`

## 📘 Objective

In this exercise, I learned how to use the `rate()` function in PromQL to calculate how quickly a counter metric changes over time. The `rate()` function is commonly used with counter metrics because counters continuously increase while a system is running.
---
# 📝 Task Performed

The goal was to measure how much time CPU core `0` spent in the `idle` state during the last 5 minutes.
The metric used:

```text
node_cpu_seconds_total
```

This metric stores the total accumulated CPU time since system startup.

---

# ▶️ Query Used

```promql
rate(node_cpu_seconds_total{cpu="0",mode="idle"}[5m])
```

---

# 🔍 Understanding the Query

| Component | Purpose |
|---|---|
| `node_cpu_seconds_total` | CPU counter metric |
| `cpu="0"` | Selects CPU core 0 |
| `mode="idle"` | Filters idle CPU time |
| `[5m]` | Looks at the last 5 minutes of data |
| `rate()` | Calculates the per-second growth rate |

---
<img width="1365" height="316" alt="image" src="https://github.com/user-attachments/assets/04b04f0b-39b9-4439-8994-3233166633c5" />

# ✅ Observed Output

The query returned decimal values such as:

```text
0.85
```
---

# 📊 Interpretation

The returned value represents the percentage of time the CPU remained idle.

Example:

```text
0.85 = 85% idle
```

This means:

- CPU was idle 85% of the time
- CPU utilization was approximately 15%

---

# 💡 Why `rate()` Is Important

The metric `node_cpu_seconds_total` is a counter metric, which means it only increases over time.

A raw counter value might look like this:

```text
123456.78
```

However, this number alone is not very useful because it simply represents accumulated CPU seconds since boot.

The `rate()` function converts this cumulative value into a meaningful real-time measurement by answering:

```text
How fast is the counter increasing right now?
```

This makes it easier to monitor actual system activity and utilization trends.

---

# 📚 Key Learning Outcome

Through this exercise, I learned:

- How to work with counter metrics in Prometheus
- Why `rate()` is required for meaningful analysis
- How range vectors like `[5m]` are used
- How to interpret CPU idle percentages
- How PromQL transforms raw monitoring data into operational insights

---

# 🚀 Practical Use Cases

The `rate()` function is widely used for monitoring:

- CPU utilization
- Network traffic rates
- Disk I/O activity
- Request throughput
- Application request counters
- Error rates

It is one of the most important functions in PromQL for real-time infrastructure analysis.



# Step 4: Working with Aggregation Functions

## 📌 Objective

I used aggregation operators to combine multiple time-series values into a single metric.

The focus was calculating average CPU idle time across all processor cores.

---

## ▶️ Query Used

```promql
avg(rate(node_cpu_seconds_total{mode="idle"}[5m]))
```

---
<img width="1365" height="309" alt="image" src="https://github.com/user-attachments/assets/74bbc6ee-85b6-4e26-874f-120eccae3fdd" />
The output represented the average idle percentage of the system CPU.

## ✅ What I Learned

This query:
1. Calculated the CPU idle rate over a 5-minute interval
2. Collected metrics from all CPU cores
3. Averaged the values into a single result

---

# 📚 Common Aggregation Functions

| Function | Purpose |
|---|---|
| `sum()` | Adds values together |
| `avg()` | Calculates average value |
| `max()` | Returns highest value |
| `min()` | Returns lowest value |
| `count()` | Counts time series |

---

# Step 5: Calculating CPU Usage Percentage

## 📌 Objective

Next, I converted idle CPU metrics into actual CPU utilization percentages.

The idea was simple:

```text
CPU Usage = 100 - Idle Percentage
```

---

## ▶️ Query Used

```promql
100 - (avg(rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)
```

---
<img width="1365" height="308" alt="image" src="https://github.com/user-attachments/assets/7cb7e390-9d01-400d-80c1-9fed07b814fb" />

## ✅ Result

The query produced a percentage value representing current CPU usage.

Example:

```text
15.9
```

This indicated that the CPU was approximately 15% utilized.

---

# 🔍 Query Explanation

| Expression | Purpose |
|---|---|
| `rate()` | Calculates change over time |
| `avg()` | Combines all CPU cores |
| `* 100` | Converts decimal to percentage |
| `100 - value` | Converts idle percentage into usage percentage |

---

# Step 6: Creating a Static Threshold Query

## 📌 Objective

In this task, I created a basic threshold-based monitoring query to detect high CPU usage.

The goal was to trigger a condition whenever CPU utilization exceeded 80%.

---

## ▶️ Query Used

```promql
(100 - (avg(rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)) > 80
```

---

## ✅ Expected Behavior

- If CPU usage remained below 80%, no result appeared
- If CPU usage crossed 80%, the query returned a value

This demonstrated how traditional monitoring systems use static thresholds for alerting.

---

# ⚠️ Limitations of Static Thresholds

While threshold-based alerts are simple to configure, they also introduce several challenges:

- Fixed thresholds may not fit every workload
- Alerts lack operational context
- Frequent false positives can create alert fatigue
- Static rules cannot adapt to traffic patterns or seasonal changes
- Monitoring becomes reactive rather than predictive

---

# Step 7: Additional PromQL Practice

## 📌 Objective

To strengthen my understanding of PromQL, I experimented with several additional infrastructure queries.

---

# 1️⃣ Available Memory in GB

## ▶️ Query

```promql
node_memory_MemAvailable_bytes / 1024 / 1024 / 1024
```

This converts available memory from bytes into gigabytes.
<img width="1365" height="322" alt="image" src="https://github.com/user-attachments/assets/4cd033b5-ce1a-4592-a126-402092c622dc" />

---

# 2️⃣ Memory Usage Percentage

## ▶️ Query

```promql
100 - ((node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes) * 100)
```
<img width="1365" height="297" alt="image" src="https://github.com/user-attachments/assets/0f3be1f3-a028-4596-884d-f1bfdbfc6c8b" />

This calculates the percentage of memory currently in use.

---

# 3️⃣ Network Receive Rate

## ▶️ Query

```promql
rate(node_network_receive_bytes_total[5m])
```

This shows incoming network traffic per second.
<img width="1365" height="341" alt="image" src="https://github.com/user-attachments/assets/f1ad5ccc-f27e-4240-935f-9a169d80ef6b" />

---

# 4️⃣ Disk Space Availability

## ▶️ Query

```promql
node_filesystem_avail_bytes{mountpoint="/"} / 1024 / 1024 / 1024
```

This displays remaining disk space for the root filesystem in gigabytes.

---

# 📈 Visualization Practice

I also explored the **Graph** view in Prometheus to observe how metrics changed over time.

This helped visualize:

- Usage spikes
- Trends
- Resource fluctuations
- System behavior patterns

---
<img width="1348" height="621" alt="image" src="https://github.com/user-attachments/assets/b6eba16a-b3cc-4382-b180-cd8e95c0f94a" />

# 🏗️ Understanding the Monitoring Workflow

Through this project exercise, I understood how PromQL fits into the observability and AIOps workflow.

```text
Metrics Collection → Query Analysis → Operational Insights
```

PromQL acts as the analytical layer that converts raw metrics into actionable information.

---
## ✅ Outcomes Achieved

By completing this exercise, I successfully:

- Learned the basics of PromQL
- Queried and filtered Prometheus metrics
- Used labels for metric selection
- Calculated rates and percentages
- Aggregated infrastructure metrics
- Built threshold-based monitoring queries
- Explored system metrics through Prometheus
- Understood the gap between static monitoring and AI-driven observability

---
# ⚠️ Challenges with Traditional Monitoring

This exercise also highlighted the limitations of manual monitoring approaches:

- Engineers must manually define thresholds
- Systems cannot automatically understand context
- Monitoring remains reactive
- Large environments generate excessive alert noise

---

# 🤖 Why AI-Driven Monitoring Matters

Modern AIOps platforms improve observability by introducing machine learning and adaptive analysis techniques.

AI-driven monitoring systems can:

- Learn normal system behavior automatically
- Detect anomalies without fixed thresholds
- Predict outages before failures occur
- Reduce alert fatigue using intelligent filtering
- Improve operational efficiency

---

# 🚀 Next Learning Goals

In the next phase of the project, I plan to explore:

- AI-powered anomaly detection
- Predictive monitoring techniques
- Statistical analysis of infrastructure metrics
- Machine learning integration with Prometheus data
- Automated operational insights

---

# 🌟 Final Summary

This project exercise provided hands-on experience with Prometheus and PromQL for infrastructure monitoring and analysis.

I now understand how monitoring data can be transformed into operational insights and how these concepts form the foundation for advanced AIOps and anomaly detection systems.
