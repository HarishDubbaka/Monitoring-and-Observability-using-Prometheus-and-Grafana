# 🚨 Prometheus Alerting with Alertmanager

## 📌 Overview

Till now, we have seen how metrics collected by Prometheus are visualized using Grafana dashboards. While dashboards are useful for monitoring system health, they require continuous human attention.

❗ However, in real-world production systems:
- Engineers cannot monitor dashboards 24/7
- Critical issues may go unnoticed
- Delayed response can lead to downtime

👉 To solve this, we use **Alerting** with Prometheus and Alertmanager.

---

## 🧠 Architecture Components

### 🔹 Prometheus
- Collects metrics from applications and infrastructure
- Evaluates alert rules using PromQL

### 🔹 Alertmanager
- Handles alerts sent by Prometheus
- Responsible for:
  - Deduplication
  - Grouping
  - Silencing
  - Routing

### 🔹 Grafana
- Used for visualization (dashboards only, no alert processing here in this flow)

---

## 🔄 Alerting Workflow

```text
[Application Metrics]
        ↓
   Prometheus (Scraping)
        ↓
   Alert Rules Evaluation
        ↓
   Alert Triggered 🚨
        ↓
   Alertmanager
        ↓
   Notification Channels (Email / Slack / PagerDuty)
````

## 📊 Example Alerts (Kubernetes)

### 🔹 Pod CrashLoopBackOff

```promql
kube_pod_container_status_waiting_reason{reason="CrashLoopBackOff"} > 0
```

---

### 🔹 High CPU Usage

```promql
sum(rate(container_cpu_usage_seconds_total[5m])) > 0.8
```

---

### 🔹 Node Down

```promql
up{job="node-exporter"} == 0
```

---

## 🔔 Alertmanager Features Explained

### ✅ Deduplication

* Prevents duplicate alerts for the same issue

### ✅ Grouping

* Combines multiple alerts into one notification

### ✅ Silencing

* Temporarily mute alerts (e.g., during maintenance)

### ✅ Routing

* Sends alerts to different teams based on labels

---

## 📬 Notification Channels

Alertmanager supports multiple integrations:

* 📧 Email
* 💬 Slack
* 📱 PagerDuty
* 🔔 Webhooks

---

## 🚀 Real-World Scenario

👉 Example: Kubernetes Pod Failure

1. Pod crashes
2. Prometheus detects using:

   ```promql
   kube_pod_status_phase{phase="Failed"} > 0
   ```
3. Alert fires after defined duration
4. Alertmanager:

   * Groups alerts
   * Sends notification to DevOps team
5. Team responds immediately

---

## 🧩 Key Benefits

* ✅ Automated monitoring (no manual effort)
* ✅ Faster incident response
* ✅ Reduced downtime
* ✅ Scalable alerting system
* ✅ Centralized alert management

---

## 🧠 Summary

| Component    | Role                               |
| ------------ | ---------------------------------- |
| Prometheus   | Collects metrics & triggers alerts |
| Alertmanager | Manages and routes alerts          |
| Grafana      | Visualizes metrics                 |

---

## 📚 Conclusion

Alerting transforms monitoring from a passive system into an **active response system**.

Instead of watching dashboards all day:
👉 Systems notify you when something goes wrong.

This is a critical part of **production-grade observability**.

