## 🔹 Introduction to Observability

**Observability** is the capability to understand a system’s internal state by analyzing the data it produces, such as **metrics, logs, and traces**. It allows teams to:

* Monitor system performance
* Detect issues early
* Troubleshoot problems efficiently

Modern applications rely on observability to ensure reliability. Instead of guessing what went wrong, engineers can gain **real-time insights** into system behavior.

**Common Tools:**

* **Prometheus** – Collects and stores metrics data.
* **Grafana** – Visualizes metrics on interactive dashboards to identify trends and anomalies.

### 🔹 The Three Pillars of Observability

| Pillar      | What it Provides                                | Example                                    |
| ----------- | ----------------------------------------------- | ------------------------------------------ |
| **Metrics** | Numerical data representing system performance  | CPU usage, request count                   |
| **Logs**    | Detailed records of system events               | Error logs, transaction details            |
| **Traces**  | End-to-end tracking of requests across services | User request journey through microservices |

> By combining metrics, logs, and traces, teams move from reactive troubleshooting to **proactive monitoring**, improving system stability and user experience.

---

## 🆚 Monitoring vs Observability

**Key Difference:**

* **Monitoring** = “When and what” of a system issue
* **Observability** = “Why and how” the issue occurred

| Category    | Monitoring                         | Observability                                         |
| ----------- | ---------------------------------- | ----------------------------------------------------- |
| **Focus**   | Checking if systems are working    | Understanding why issues happen                       |
| **Data**    | Metrics (CPU, memory, error rates) | Logs, metrics, traces for a full picture              |
| **Alerts**  | Notifies when something goes wrong | Correlates events to find root causes                 |
| **Example** | CPU > 90% triggers alert           | Tracing a slow website request to find the bottleneck |
| **Insight** | Identifies potential issues early  | Diagnoses issues and explains system behavior         |

**Analogy:**

* Monitoring = Alarm system 🚨 → Tells you something is wrong
* Observability = Full investigation toolkit 🔍 → Helps find the root cause

---

## 🔭 Observability Includes Monitoring

Yes, **monitoring is a subset of observability**.

* **Monitoring** focuses on tracking specific metrics and alerting on predefined conditions.
* **Observability** provides a comprehensive understanding of the system, analyzing **logs, metrics, and traces** to uncover root causes.

---

## 🖥️ What Can Be Monitored?

* **Infrastructure:** CPU, memory, disk I/O, network traffic
* **Applications:** Response times, error rates, throughput
* **Databases:** Query performance, connection pool usage, transaction rates
* **Network:** Latency, packet loss, bandwidth usage
* **Security:** Unauthorized access attempts, firewall logs, vulnerability scans

---

## 👀 What Can Be Observed?

* **Logs:** Detailed records of events and transactions
* **Metrics:** Quantitative data like CPU load, memory usage, request counts
* **Traces:** Data showing request flow through multiple services

---
