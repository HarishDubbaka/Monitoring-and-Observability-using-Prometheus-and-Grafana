# 📊 Metrics, Monitoring & Prometheus

Understanding **Metrics**, **Monitoring**, and **Prometheus** is essential for building reliable and observable systems, especially in modern environments like **Kubernetes and cloud-native applications**.

---

# 📈 What are Metrics?

**Metrics** are numerical measurements collected over time that help you understand the **performance and health of a system**.

In simple terms:

> **Metrics = Numbers that show how a system is behaving**

### 🔹 Examples of Metrics

Common metrics collected in systems and applications:

- 🖥 **CPU Usage** – how much processor is being used
- 💾 **Memory Usage** – how much RAM is consumed
- 🌐 **Request Rate** – number of requests per second
- ❌ **Error Rate** – number of failed requests
- 💿 **Disk I/O** – read/write operations on disk

Metrics help engineers understand **system performance trends over time**.

---

# 🖥 What is Monitoring?

**Monitoring** is the process of **continuously observing and tracking the health, performance, and status of systems, applications, or infrastructure**.

It helps teams understand **what is happening in real-time** and quickly detect problems.

### 🔹 Simple Definition

> **Monitoring = Collecting and analyzing system data to ensure everything is working properly**

### 🔹 What Monitoring Tracks

Monitoring tools typically track:

- 🖥 CPU Usage
- 💾 Memory Usage
- 🌐 Network Traffic
- ⚡ Application Response Time
- ❌ Error Rates
- 📦 Container / Pod Health (in Kubernetes)

Monitoring enables **proactive detection of system issues before they impact users**.

---

# 📊 Metrics vs Monitoring

| Aspect | **Metrics** | **Monitoring** |
|------|-------------|-------------|
| **Definition** | Numerical data that measures system performance | The process of collecting, analyzing, and tracking metrics |
| **Purpose** | Provides raw performance data | Uses metrics to observe system health |
| **Example** | CPU usage = 70%, Memory usage = 4GB | Alert when CPU usage exceeds 90% |
| **Type** | Data | Process |
| **Tools** | **Prometheus** collects metrics | Monitoring uses tools like **Prometheus** and dashboards like **Grafana** |

---

# 🧠 Simple Explanation

- **Metrics** → The **numbers/data** about system performance  
- **Monitoring** → The **process of watching and analyzing those numbers**

---

# 🌍 Real-World Example

### Scenario

- **Metric:** CPU Usage = 85%
- **Monitoring:** System tracks CPU usage and **sends an alert when it crosses 90%** 🚨

This allows teams to **take action before the system becomes unstable**.

---

# ⚡ Use Case: Website Traffic Spike & Server Overload

### 💥 Problem Scenario

You have a web application running on a server (VM or bare metal).

Suddenly:

- Traffic increases (sale, promotion, viral content)
- Server becomes slow
- Users experience delays or failures

### 🎯 What You Want

- Monitor traffic and system health
- Detect unusual spikes
- Get alerted before the server crashes

Example:

CPU usage becomes high in a **Kubernetes cluster**, but you don't know which service is causing it.

You also want to **predict and alert before CPU reaches a critical threshold (like 85%)**.

This is exactly where **Prometheus becomes essential**.

---

# 🚀 What is Prometheus?

**Prometheus** is an **open-source monitoring and alerting toolkit** originally developed at **SoundCloud in 2012**.

It is now a **CNCF (Cloud Native Computing Foundation) graduated project** and widely used for monitoring **microservices, containers, and Kubernetes environments**.

Prometheus is designed for **reliability and scalability in dynamic cloud environments**.

---

# ⭐ Key Features of Prometheus

### 📊 Time-Series Database
Prometheus stores all metrics as **time-series data**, identified by:

- Metric name
- Labels (key-value pairs)

This allows **powerful and flexible querying**.

---

### 🔄 Pull-Based Model
Prometheus **scrapes metrics from targets** at regular intervals instead of relying on systems to push data.

Benefits:

- More reliable
- Easier configuration
- Better control over monitoring

---

### 🔎 PromQL Query Language
Prometheus includes a powerful query language called **PromQL**.

It allows you to:

- Aggregate metrics
- Filter data
- Perform calculations
- Analyze trends

---

### 🔍 Service Discovery
Prometheus supports **automatic discovery of monitoring targets**.

Examples:

- Kubernetes
- Consul
- AWS EC2
- Static configurations

This is critical for **dynamic environments where services frequently change**.

---

### ⚙ No External Dependencies
Prometheus runs as a **single binary**, making it simple to deploy and operate.

---

### 🚨 Built-in Alerting
Prometheus integrates with **Alertmanager** to handle alerts.

Features include:

- Alert deduplication
- Alert grouping
- Alert routing
- Notification integrations

Alerts can be sent to:

- Email
- Slack
- PagerDuty
- Webhooks

---

# 🤔 Why Do We Need Prometheus?

Prometheus helps you:

- Automatically collect system metrics
- Monitor application performance
- Detect issues early
- Trigger alerts before failures happen

It is a **must-have tool for modern cloud-native systems like Kubernetes**.

---

# 🏗 Prometheus Architecture

Prometheus collects metrics from instrumented applications and infrastructure components.

It then:

1. Scrapes metrics from targets
2. Stores them in a time-series database
3. Runs queries and alert rules
4. Sends alerts via Alertmanager
5. Visualizes data using tools like Grafana


![Image Alt](https://github.com/HarishDubbaka/Monitoring-and-Observability-using-Prometheus-and-Grafana/blob/864bb2cae1d96b678f67bb88a083ccce3a1c03de/Day02/prometheus-architecture.gif)


---

# 🧩 Components of Prometheus Architecture

## Prometheus Server

The **Prometheus Server** is the core component.

Responsibilities:

- Scrapes metrics from targets
- Stores metrics in **Time-Series Database (TSDB)**
- Executes **PromQL queries**
- Evaluates alert rules

---

## Targets and Exporters

**Targets** are the systems from which Prometheus collects metrics.

Examples:

- Servers
- Applications
- Kubernetes Pods
- Databases

Metrics are typically exposed on:

```

/metrics

```

### Exporters

Exporters convert system metrics into a **Prometheus-readable format**.

Example:

- **Node Exporter** → CPU, memory, disk metrics

---

## Service Discovery

Prometheus supports both:

### Static Discovery
Used for systems with fixed IPs.

### Dynamic Discovery
Used in dynamic environments like:

- Kubernetes
- Consul
- AWS EC2

Prometheus automatically discovers new targets.

---

## Pushgateway

Prometheus primarily uses a **pull model**, but some jobs are **short-lived**.

Example:

- Batch jobs
- CI/CD pipelines

These jobs push metrics to **Pushgateway**, which Prometheus then scrapes.

---

## Alert Manager

**Alertmanager** handles alerts generated by Prometheus.

Features include:

- Deduplication
- Grouping
- Silencing
- Routing

Alerts can be sent to:

- Slack
- Email
- PagerDuty
- Webhooks

---

## PromQL

**PromQL** is the query language used to retrieve and analyze metrics stored in Prometheus.

It is used in:

- Prometheus UI
- Grafana dashboards
- CLI tools

---

## Client Libraries

Client libraries allow developers to **instrument applications** to expose custom metrics.

Prometheus provides libraries for multiple languages such as:

- Go
- Python
- Java
- Node.js

---

# 📊 Visualization with Grafana

Prometheus data is often visualized using **Grafana dashboards**.

Grafana helps to:

- Build dashboards
- Visualize metrics
- Monitor system health
- Create alerts

---

# 🧾 Conclusion

Prometheus architecture is **modular, scalable, and designed for dynamic distributed systems**.

By combining:

- **Metrics**
- **Monitoring**
- **Alerting**
- **Visualization**

Prometheus provides a **complete observability solution for modern infrastructure**.

---

# 📚 References

Official Prometheus Documentation:

https://prometheus.io/docs/introduction/overview/

Download Prometheus:

https://prometheus.io/download/

