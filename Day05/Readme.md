# Set up Prometheus Alert Manager for Kubernetes Cluster with Custom Alerts

## Prerequisites

* Azure account
* Ubuntu 24.04 LTS VM instance
* Minikube, kubectl, and Helm installed
* Basic knowledge of Kubernetes

---

## Step 1: System Preparation

```bash
sudo apt update
sudo apt install curl wget apt-transport-https -y
sudo apt install docker.io -y
sudo usermod -aG docker $USER
sudo chmod 666 /var/run/docker.sock
egrep -q 'vmx|svm' /proc/cpuinfo && echo yes || echo no
sudo apt install qemu-kvm libvirt-clients libvirt-daemon-system bridge-utils virtinst libvirt-daemon
newgrp libvirt
newgrp libvirt-qemu
```

---

## Step 2: Install Minikube and kubectl

```bash
# Install Minikube
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
minikube version

# Install kubectl
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x ./kubectl
sudo mv kubectl /usr/local/bin/
kubectl version --client --output=yaml
```

---

## Step 3: Start Minikube

```bash
minikube start --vm-driver docker
minikube status
```

---

## Step 4: Install Helm

```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
helm version

# Add Prometheus Helm repo
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

---

## Step 5: Configure Prometheus, Alertmanager, and Grafana

Create a `custom-values.yaml` file:

```yaml
prometheus:
  service:
    type: NodePort
grafana:
  service:
    type: NodePort
alertmanager:
  service:
    type: NodePort
```

Install Prometheus stack:

```bash
helm upgrade --install kube-prometheus-stack prometheus-community/kube-prometheus-stack -f custom-values.yaml
```

---

## Step 6: Access Services

Check service ports:

```bash
kubectl get services
```

You should see `NodePort` services for Prometheus, Alertmanager, and Grafana.

---

## Step 7: Create Custom Alert Rules

Create `custom-alert-rules.yaml`:

```yaml
apiVersion: monitoring.coreos.com/v1
kind: PrometheusRule
metadata:
  labels:
    app: kube-prometheus-stack
    app.kubernetes.io/instance: kube-prometheus-stack
    release: kube-prometheus-stack
  name: kube-pod-not-ready
spec:
  groups:
  - name: my-pod-demo-rules
    rules:
    - alert: KubernetesPodNotHealthy
      expr: sum by (namespace, pod) (kube_pod_status_phase{phase=~"Pending|Unknown|Failed"}) > 0
      for: 1m
      labels:
        severity: critical
      annotations:
        summary: Kubernetes Pod not healthy (instance {{ $labels.instance }})
        description: "Pod {{ $labels.namespace }}/{{ $labels.pod }} has been in a non-running state for longer than 15 minutes.\n  VALUE = {{ $value }}\n  LABELS = {{ $labels }}"
    - alert: KubernetesDaemonsetRolloutStuck
      expr: kube_daemonset_status_number_ready / kube_daemonset_status_desired_number_scheduled * 100 < 100 or kube_daemonset_status_desired_number_scheduled - kube_daemonset_status_current_number_scheduled > 0
      for: 10m
      labels:
        severity: warning
      annotations:
        summary: Kubernetes DaemonSet rollout stuck (instance {{ $labels.instance }})
        description: "Some Pods of DaemonSet {{ $labels.namespace }}/{{ $labels.daemonset }} are not scheduled or not ready\n  VALUE = {{ $value }}\n  LABELS = {{ $labels }}"
    - alert: ContainerHighCpuUtilization
      expr: (sum(rate(container_cpu_usage_seconds_total{container!=""}[5m])) by (pod, container) / sum(container_spec_cpu_quota{container!=""}/container_spec_cpu_period{container!=""}) by (pod, container) * 100) > 80
      for: 2m
      labels:
        severity: warning
      annotations:
        summary: Container High CPU utilization (instance {{ $labels.instance }})
        description: "Container CPU utilization is above 80%\n  VALUE = {{ $value }}\n  LABELS = {{ $labels }}"
    - alert: ContainerHighMemoryUsage
      expr: (sum(container_memory_working_set_bytes{name!=""}) BY (instance, name) / sum(container_spec_memory_limit_bytes > 0) BY (instance, name) * 100) > 80
      for: 2m
      labels:
        severity: warning
      annotations:
        summary: Container High Memory usage (instance {{ $labels.instance }})
        description: "Container Memory usage is above 80%\n  VALUE = {{ $value }}\n  LABELS = {{ $labels }}"
    - alert: KubernetesContainerOomKiller
      expr: (kube_pod_container_status_restarts_total - kube_pod_container_status_restarts_total offset 10m >= 1) and ignoring (reason) min_over_time(kube_pod_container_status_last_terminated_reason{reason="OOMKilled"}[10m]) == 1
      for: 0m
      labels:
        severity: warning
      annotations:
        summary: Kubernetes Container oom killer (instance {{ $labels.instance }})
        description: "Container {{ $labels.container }} in pod {{ $labels.namespace }}/{{ $labels.pod }} has been OOMKilled {{ $value }} times in the last 10 minutes.\n  VALUE = {{ $value }}\n  LABELS = {{ $labels }}"
    - alert: KubernetesPodCrashLooping
      expr: increase(kube_pod_container_status_restarts_total[1m]) > 3
      for: 2m
      labels:
        severity: warning
      annotations:
        summary: Kubernetes pod crash looping (instance {{ $labels.instance }})
        description: "Pod {{ $labels.namespace }}/{{ $labels.pod }} is crash looping\n  VALUE = {{ $value }}\n  LABELS = {{ $labels }}"
```

---

## Step 8: Deploy a Test Application

```bash
kubectl run nginx-pod --image=nginx:lates3
```

> This pod will fail (ImagePullBackOff) and trigger your custom alert rules.

Optional: Keep a failing pod for testing:

```bash
kubectl run test-fail --image=nginx:doesnotexist
```

---

## Step 9: Configure Alertmanager for Email Notifications

Create a secret `alertmanager-secret.yaml`:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: alertmanager-kube-prometheus-stack-alertmanager
  namespace: default
type: Opaque
stringData:
  alertmanager.yaml: |
    global:
      resolve_timeout: 5m
      smtp_smarthost: 'smtp.gmail.com:587'
      smtp_from: 'your-email@gmail.com'
      smtp_auth_username: 'your-email@gmail.com'
      smtp_auth_password: 'your-email-password'
      smtp_require_tls: true

    route:
      group_by: ['alertname']
      group_wait: 30s
      group_interval: 5m
      repeat_interval: 1h
      receiver: 'email-notifications'

    receivers:
      - name: 'email-notifications'
        email_configs:
          - to: 'your-email@gmail.com'
            send_resolved: true
```

Apply it:

```bash
kubectl apply -f alertmanager-secret.yaml
```

---

## Step 10: Verify Alerts

* Open Prometheus and Alertmanager UIs
* Check for triggered alerts due to the test pod failures
* Alerts will resolve automatically once the pod issues are fixed

![Image Alt](image_url).

---


# 🔹 Why Alerts Still Might Not Fire

Even though the Secret is configured, alerts won’t trigger unless:

1. **PrometheusRule is active and loaded**

   * `kubectl get prometheusrules -A`
   * `kubectl describe prometheusrules kube-pod-not-ready`
2. **Prometheus is scraping metrics**

   * `kube-state-metrics` must be running
   * Query metrics in Prometheus UI:

     ```promql
     kube_pod_status_phase{phase=~"Pending|Unknown|Failed"}
     ```
3. **Pods are actually unhealthy**

   * `kubectl get pods -A`
   * If pods are `Running`, no alert fires
4. **Prometheus → Alertmanager communication works**

   * Check Prometheus alerting rules in **Status → Rules**
   * Active firing alerts should appear → then Alertmanager sends notifications

---

# 🔹 Quick Test to Verify Everything

1. Create a pod that will fail:

```bash
kubectl run test-fail --image=nginx:doesnotexist
```

2. In Prometheus UI:

```promql
sum by(namespace,pod)(kube_pod_status_phase{phase=~"Pending|Unknown|Failed"}) > 0
```

* Should return `1` → alert **should fire**
* Alertmanager UI should show **active alert**
* Email notification should be sent if receiver is configured

