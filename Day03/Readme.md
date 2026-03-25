# 📅 Day 03 – PromQL & Kubernetes Observability using Prometheus and Grafana

## 📊 Metrics in Prometheus:
- Metrics in Prometheus are the core data objects that represent measurements collected from monitored systems.
- These metrics provide insights into various aspects of **system performance, health, and behavior**.

## 🏷️ Labels:
- Metrics are paired with Labels.
- Labels are key-value pairs that allow you to differentiate between dimensions of a metric, such as different services, instances, or endpoints.


## 🔍 Example:
```bash
container_cpu_usage_seconds_total{namespace="kube-system", endpoint="https-metrics"}
```
- `container_cpu_usage_seconds_total` is the metric.
- `{namespace="kube-system", endpoint="https-metrics"}` are the labels.


## 🛠️ What is PromQL?
- PromQL (Prometheus Query Language) is a powerful and flexible query language used to query data from Prometheus.
- It allows you to retrieve and manipulate time series data, perform mathematical operations, aggregate data, and much more.

- 🔑 Key Features of PromQL:
    - Selecting Time Series: You can select specific metrics with filters and retrieve their data.
    - Mathematical Operations: PromQL allows for mathematical operations on metrics.
    - Aggregation: You can aggregate data across multiple time series.
    - Functionality: PromQL includes a wide range of functions to analyze and manipulate data.


## 🚀 Overview

In this lab, we set up a **complete Kubernetes monitoring stack** using:

* **Prometheus** → Metrics collection & querying
* **Grafana** → Dashboard visualization
* **Node Exporter** → Node-level metrics
* **kube-state-metrics** → Kubernetes object state

---

## 📦 Prerequisites

* Running Kubernetes cluster (Kind)
* Helm installed
* kubectl configured

---

## ⚙️ Setup Monitoring Stack

### 🔹 Generate Manifests

```bash
helm template monitoring prometheus-community/kube-prometheus-stack \
  -n monitoring \
  -f kube-prometheus-values.yaml > manifests.yaml
```

### 🔹 Apply Manifests

```bash
kubectl apply -f manifests.yaml
```

---

## 🔍 Verify Installation

```bash
kubectl get all -n monitoring
```

✅ Key components:

* Prometheus (StatefulSet)
* Grafana (Deployment)
* Alertmanager
* Node Exporter (DaemonSet)
* kube-state-metrics

---

## 🌐 Access Services

### 🔹 Prometheus

```bash
kubectl port-forward svc/monitoring-kube-prometheus-prometheus 9090:9090 -n monitoring
```

👉 Open: [http://localhost:9090](http://localhost:9090)

---

### 🔹 Grafana

```bash
kubectl port-forward svc/monitoring-grafana 3000:3000 -n monitoring
```

👉 Open: [http://localhost:3000]

**Login:**

```
username: admin
password: prom-operator
```

---

## 🐳 Access Kind Cluster Nodes

👉 Nodes in Kind are **Docker containers**

### List Nodes

```bash
docker ps
```

### Exec into Node

```bash
docker exec -it monitoring-cluster-control-plane bash
docker exec -it monitoring-cluster-worker bash
```

---

## 📡 Access Metrics Endpoints

### 🔹 Node Exporter Metrics

```bash
curl 10.96.229.71:9100/metrics  -- which show the metirs of node 
```

### 🔹 Kube-State-Metrics

```bash
curl 10.96.190.152:8080/metrics -- which shows the metrices of kube
```

---

## 📊 Understanding Metrics Sources

### 🔹 Node Exporter

* Collects **system metrics**
* Source: Linux kernel

Examples:

```
node_cpu_seconds_total
node_memory_MemAvailable_bytes
```

---

### 🔹 Kube-State-Metrics

* Collects **Kubernetes object state**
* Source: API Server

Examples:

```
kube_pod_status_phase
kube_deployment_spec_replicas
```

---

## ⚡ Key Difference

| Feature | Node Exporter  | Kube-State-Metrics |
| ------- | -------------- | ------------------ |
| Type    | Resource Usage | Object State       |
| Source  | OS             | Kubernetes API     |
| Scope   | Node           | Cluster            |

---

## 💥 Simulating Failure (CrashLoop)

```bash

kubectl run crashloop-demo \
  --image=busybox \
  --restart=Always \
  -- /bin/sh -c "exit 1"

---

Now check:

kubectl get pods

👉 You should see:

crashloop-demo   0/1   CrashLoopBackOff
```
---

## 🔍 Prometheus Queries (PromQL)

### 📌 Total Pods

```promql
count(kube_pod_info)
```

---

### 📌 Pods by Status

```promql
sum by (phase) (kube_pod_status_phase)
```

---

### 📌 Running Pods

```promql
sum(kube_pod_status_phase{phase="Running"})
```

---

### 📌 Failed Pods

```promql
sum(kube_pod_status_phase{phase="Failed"})
```

---

### 📌 Deployments Health

```promql
kube_deployment_status_replicas_available
/
kube_deployment_spec_replicas
```

---

### 📌 Node Ready Status

```promql
kube_node_status_condition{condition="Ready", status="true"}
```

---

### 📌 Pods per Namespace

```promql
count by (namespace) (kube_pod_info)
```

---

### 📌 Restart Count 🚨

```promql
sum by (pod) (kube_pod_container_status_restarts_total)
```

---

## 📊 Grafana Dashboard

* Created **custom dashboards**
* Visualized:

  * Pod status
  * Restart counts
  * Node health
  * Deployment health

---

## 🎯 Key Learnings

* Prometheus collects **time-series metrics**
* Grafana visualizes data using dashboards
* Node Exporter → **Node performance**
* kube-state-metrics → **Cluster state**
* PromQL is used for **real-time querying**
* Failure simulation helps in **observability testing**

---

✅ **Production Grafana dashboard templates**


