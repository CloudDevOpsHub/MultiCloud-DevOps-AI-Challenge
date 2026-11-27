# Day 1 — Prometheus & Grafana Session Summary

## Key Outcomes

- Understood why **continuous monitoring** is mandatory in production environments (impossible to manually monitor 100+ servers, local logs are lost when auto-scaling replaces or terminates nodes, and hackers can delete local logs).
- Learned the **three types of monitoring**: Infrastructure Monitoring, Application Monitoring, and Network Monitoring.
- Compared the **monitoring tools landscape**: Prometheus + Grafana (open-source, best fit for Kubernetes), ELK Stack, Splunk, Dynatrace, AppDynamics, Datadog, AWS CloudWatch, Nagios, Zabbix, and SolarWinds.
- Learned why **Helm** (package manager for Kubernetes) and **Artifact Hub** (`artifacthub.io`) are used to deploy `kube-prometheus-stack` with `values.yaml` customization.
- Deployed **Prometheus & Grafana** on a **GKE Standard** cluster in an isolated `monitor` namespace using `helm install prometheus prometheus-community/kube-prometheus-stack -n monitor`.
- Accessed the **Grafana UI** (port `3000`) via `kubectl port-forward` / Cloud Shell Web Preview and by exposing the Grafana Service as a `LoadBalancer`, retrieved the Base64-decoded `admin` password from Kubernetes Secrets, and imported a **community dashboard** (ID `11594`).

---

## Decisions Made

- **Use GKE Standard (not GKE Autopilot) for the monitoring lab:** GKE Autopilot blocks certain scheduler/node permissions (`Forbidden` errors) required by `kube-prometheus-stack`, whereas GKE Standard works cleanly.
- **Deploy monitoring into a dedicated `monitor` namespace:** Keeps Prometheus, Grafana, `kube-state-metrics`, and `node-exporter` isolated from application workloads.
- **Use Helm (`kube-prometheus-stack`) instead of manual YAML manifests:** Simplifies installation, upgrades, and configuration via `values.yaml`.
- **Expose Grafana via `LoadBalancer` (or `port-forward` for quick testing):** Allows direct browser access to the Grafana dashboard on port `3000`.

---

## 1. Why Continuous Monitoring Is Required

- **Scale:** Manually checking 100+ production servers is impossible.
- **Ephemeral Nodes & Auto-Scaling:** If a server/node goes down or is replaced by auto-scaling, local logs inside that machine are deleted.
- **Security & Audit Trail:** Attackers/hackers can delete local logs on a compromised server; centralized monitoring preserves an audit trail and triggers alerts.
- **Cost & Storage Optimization:** Keeping 3–12 months of logs on application servers requires expensive heavy disks/machines. Instead, keep application machines small and push logs/metrics to a centralized location every few seconds.
- **Common Real-Time Issues Detected:**
  - Disk full
  - High CPU or memory utilization
  - Low memory utilization (wasted resources/cost)
  - Hardware high temperature
  - Network outages
  - Application bugs and failures

---

## 2. Monitoring Tools Landscape

| Tool | Type / Focus | Key Notes from Session |
| :--- | :--- | :--- |
| **Prometheus + Grafana** | Open-source, free | Best fit for **Kubernetes** environments; Prometheus scrapes metrics, Grafana visualizes and alerts. |
| **ELK Stack** | Elasticsearch, Logstash/agent, Kibana | Centralized logging/monitoring; harder to manage because if one component goes down, the stack can go down. |
| **Splunk** | Enterprise logging | Popular enterprise solution; uses heavy forwarders installed on servers. |
| **Dynatrace / AppDynamics / Datadog** | Enterprise APM / end-to-end | Full-stack enterprise monitoring with license/billing limits. |
| **AWS CloudWatch** | Managed AWS monitoring | Purely managed monitoring for AWS services. |
| **Nagios / Zabbix / SolarWinds** | Legacy / infra & network | Older monitoring tools; Nagios is used for disk, traffic, firewall, and network devices. |

> **Interview Tip:** If asked why a company uses two monitoring tools, explain that one is an **enterprise solution** (for production/compliance) and the other is **internally managed** (e.g., open-source) for Dev/QA environments.

---

## 3. Three Types of Monitoring

### 1. Infrastructure Monitoring
- **Scope:** VMs, servers, databases, containers, CPU, memory, and disk.
- **How it works:** An **agent** is installed on the infrastructure to collect metrics/logs and send them to a central collector for analysis, visualization, and alerting.

### 2. Application Monitoring
- **Scope:** Application logs, access logs, connectivity logs, database transactions, APIs, SLAs, error/failure logs, user response times, and session-wise tracking.
- **Debugging Note:** When investigating user-reported errors (e.g., 11-device login session issues), always ask for screenshots to trace the exact session and error state.

### 3. Network Monitoring
- **Scope:** Throughput, network I/O, packet count, packet failures, data loss, switches, and routers.
- **How it works:** Often **agentless**, using **SNMP** or APIs.

---

## 4. Helm & CNCF Ecosystem

- **Why Helm?** `apt` and `yum` are package managers for Linux VMs, whereas **Helm** is the package manager for **Kubernetes**. It installs, upgrades, and configures Kubernetes applications using **Charts** and **`values.yaml`** customization.
- **Artifact Hub (`artifacthub.io`):** Formerly Helm Hub; central repository hosting 18,000+ Helm charts, including `kube-prometheus-stack`.
- **CNCF (Cloud Native Computing Foundation):** Open-source community maintaining cloud-native tools such as Kubernetes, Helm, ArgoCD, Prometheus, and Grafana.
- **Helm Architecture Note:** The Helm client talks directly to the Kubernetes API. **Tiller** was the server-side daemon in Helm v2 and was removed in Helm v3+.
- **Checking Helm:** Run `helm version` in Cloud Shell (v4.3 shown in session).

---

## 5. Prometheus & Grafana Architecture

- **Prometheus:** Pulls/scrapes time-series metrics (JSON key-value / time-series data) from Kubernetes using:
  - **`kube-state-metrics`:** Collects Kubernetes cluster, namespace, pod, and application state.
  - **`node-exporter`:** Collects node-level CPU, memory, and OS stats.
  - Stores scraped data in local storage / Persistent Volumes.
- **Grafana:** Visualization UI that connects to Prometheus as a data source, runs **PromQL** queries, renders dashboards/graphs (similar to real-time order dashboards at Domino's or IRCTC), and sends alerts.
- **Automated in Kubernetes:** Unlike Splunk heavy forwarders on VMs, `kube-prometheus-stack` automates metric collection in Kubernetes via Helm.

---

## 6. Live Hands-On Setup on GKE (`monitor` Namespace)

1. **Create GKE Standard Cluster:** `monitoring-setup` (1 node, 30 GB disk).
2. **Create Isolated Namespace:**
   - `kubectl create namespace monitor`
   - `kubectl get ns`
3. **Add & Update Helm Repo:**
   - `helm repo add prometheus-community https://prometheus-community.github.io/helm-charts`
   - `helm repo update`
4. **Install `kube-prometheus-stack`:**
   - `helm install prometheus prometheus-community/kube-prometheus-stack -n monitor`
   - Takes ~2–4 minutes (`prometheus` is the custom release name; `kube-prometheus-stack` is the chart name).
5. **Verify Pods:**
   - `kubectl get pods -n monitor` (5 pods running).
6. **Retrieve Grafana `admin` Password:**
   - Stored in a Kubernetes Secret in the `monitor` namespace and Base64-decoded using `kubectl get secret ... -n monitor`.
7. **Access Grafana UI (Default Port `3000`):**
   - **Option A:** `kubectl port-forward` + Cloud Shell Web Preview on port `3000`.
   - **Option B:** Expose Grafana Service as `LoadBalancer` and check `kubectl get svc -n monitor` for the External IP.
8. **Import Community Dashboards:**
   - Search Grafana dashboards on Google (e.g., ID `11594` for *Detailed Pod Resources*).
   - In Grafana: **New Dashboard → Import Dashboard → Enter ID → Select Prometheus data source** (22,000+ free community dashboards available).

### Troubleshooting Covered in Class
- **Autopilot vs. Standard GKE:** GKE Autopilot throws `Forbidden` / scheduler permission errors; use GKE Standard.
- **Browser Crash / Chunk Load Error:** Can occur on small clusters when opening heavy Grafana views.
- **`No data` in Default Grafana Panels:** Happens when default PromQL queries don't match cluster components. Fix by exploring metrics in Grafana **Explore** and adjusting PromQL/math (e.g., dividing by `1000`/`10000` or converting seconds to minutes).
- **YAML Best Practice:** Start YAML files with `---` for readability.
- **HPA Performance Testing Q&A:** If pod CPU is stuck at 48% during a load test, generate more load (e.g., multiple `busybox` tabs) or use smaller cluster/pod limits.

---

## Interview Preparation Highlights

- Explain **why** centralized monitoring is needed (scale, ephemeral auto-scaled nodes, hacker log deletion, disk cost).
- Differentiate **Infrastructure, Application, and Network** monitoring clearly.
- Explain **Helm** (`Charts`, `values.yaml`, `Artifact Hub`, removal of `Tiller` in Helm v3+).
- Explain how **Prometheus** (`kube-state-metrics`, `node-exporter`, PromQL) and **Grafana** (visualization, data sources, community dashboards, alerts) work together in Kubernetes.
- **L1/L2 Career Tip:** Even if DevOps sets up the cluster (Terraform) and Prometheus/Grafana (Helm) and hands it over to L1/L2 for daily monitoring, L1/L2 engineers should pitch the full setup experience in interviews.

---

## Action Items

- Practice deploying `kube-prometheus-stack` in a `monitor` namespace on a GKE Standard cluster.
- Practice retrieving the Base64-decoded Grafana `admin` password from Kubernetes Secrets.
- Expose Grafana via `port-forward` and `LoadBalancer`, and import community dashboard ID `11594`.
- Explore metrics in Grafana **Explore** and test basic PromQL adjustments.

---

## Open Questions

- How to configure custom **Alertmanager** rules and notification channels (covered in upcoming sessions).
- How to persist Prometheus data long-term using **Persistent Volumes** in production.

---

# Interview Preparation — 20 Questions and Answers

### 1. Why is continuous monitoring mandatory in production environments?
**Answer:**
Manually checking 100+ production servers is impossible. Continuous monitoring automatically collects metrics and logs, preserves an audit trail if an auto-scaled node is terminated or compromised by a hacker, reduces storage costs on app servers, and triggers alerts on issues like high CPU/memory, disk full, or network outages.

### 2. Why shouldn't we store 3–12 months of logs directly on application servers?
**Answer:**
Storing months of logs on application servers requires expensive, heavy disks and larger machines. Instead, we keep application servers small and push logs/metrics every few seconds to a centralized monitoring location.

### 3. What happens to local logs when auto-scaling replaces or terminates a node?
**Answer:**
Local logs inside that terminated or replaced node are deleted. Centralized monitoring ensures those logs and metrics are already shipped out and preserved.

### 4. What are the three main types of monitoring?
**Answer:**
1. **Infrastructure Monitoring** (VMs, servers, databases, containers, CPU, memory, disk).
2. **Application Monitoring** (app logs, access logs, DB transactions, APIs, SLAs, response times, session tracking).
3. **Network Monitoring** (throughput, network I/O, packet count/failures, data loss, switches/routers).

### 5. How does Infrastructure Monitoring typically collect data?
**Answer:**
An **agent** is installed on the infrastructure (VM/server) that collects metrics and logs and sends them to a central collector for analysis, visualization, and alerting.

### 6. How is Network Monitoring different from Infrastructure Monitoring?
**Answer:**
Network monitoring tracks throughput, network I/O, packet count, packet failures, and data loss on switches/routers, and is often **agentless** using **SNMP** or APIs.

### 7. Why might a company use two different monitoring tools?
**Answer:**
In interviews, explain that one tool is an **enterprise solution** (e.g., Splunk, Dynatrace, Datadog) for production/compliance, while the other is an **internally managed open-source tool** (e.g., Prometheus + Grafana) used for Dev/QA environments.

### 8. Why is Prometheus + Grafana preferred for Kubernetes?
**Answer:**
Prometheus and Grafana are open-source, free, and integrate natively with Kubernetes. Using `kube-prometheus-stack`, metric collection is automated without manually installing agents like Splunk heavy forwarders on every VM.

### 9. What is a limitation of the ELK Stack mentioned in the session?
**Answer:**
ELK (Elasticsearch, Logstash, Kibana) is harder to manage because if one component goes down, the entire stack can go down.

### 10. What is Helm and why do we use it?
**Answer:**
Helm is the **package manager for Kubernetes** (similar to `apt`/`yum` for Linux). It simplifies installing, upgrading, and configuring Kubernetes applications using **Charts** and **`values.yaml`** customization.

### 11. What is Artifact Hub?
**Answer:**
Artifact Hub (`artifacthub.io`, formerly Helm Hub) is the central repository hosting 18,000+ Helm charts, including `kube-prometheus-stack`.

### 12. What was Tiller in Helm, and what happened to it?
**Answer:**
**Tiller** was the server-side daemon in Helm v2. It was removed in Helm v3+, where the Helm client talks directly to the Kubernetes API.

### 13. What is CNCF?
**Answer:**
CNCF (**Cloud Native Computing Foundation**) is the open-source community that maintains cloud-native tools such as Kubernetes, Helm, ArgoCD, Prometheus, and Grafana.

### 14. What is the role of `kube-state-metrics` in Prometheus?
**Answer:**
`kube-state-metrics` collects Kubernetes cluster, namespace, pod, and application state metrics so Prometheus can scrape them.

### 15. What is the role of `node-exporter` in Prometheus?
**Answer:**
`node-exporter` collects node-level hardware and OS statistics such as CPU and memory usage.

### 16. How does Grafana work with Prometheus?
**Answer:**
Grafana is the visualization UI that connects to Prometheus as a **data source**, runs **PromQL** queries, renders dashboards/graphs, and triggers alerts.

### 17. What does the command `helm install prometheus prometheus-community/kube-prometheus-stack -n monitor` do?
**Answer:**
It installs the `kube-prometheus-stack` chart from the `prometheus-community` repository into the `monitor` namespace, using `prometheus` as the custom release name.

### 18. What is the default port for Grafana?
**Answer:**
Port **`3000`**.

### 19. How do you retrieve the default `admin` password for Grafana after Helm installation?
**Answer:**
It is stored in a Kubernetes **Secret** in the `monitor` namespace and can be retrieved and Base64-decoded using `kubectl get secret ... -n monitor`.

### 20. What are Grafana Community Dashboards and how do you use them?
**Answer:**
Grafana provides 22,000+ free pre-built community dashboards. You can import one (e.g., ID `11594` for Detailed Pod Resources) via **New Dashboard → Import Dashboard**, entering the ID, and selecting Prometheus as the data source.

---

# Scenario-Based Interview Questions — 20 Questions and Answers

### 1. A production server is terminated by auto-scaling, and the team needs the logs from just before it crashed. Why are they missing and how do you prevent this?
**Answer:**
Local logs inside an auto-scaled node are deleted when the node is terminated. Prevent this by shipping logs and metrics every few seconds to a centralized monitoring system.

### 2. An attacker compromises a server and deletes `/var/log` to cover their tracks. How does continuous monitoring help?
**Answer:**
Centralized monitoring pushes logs/metrics off the server in real time, preserving an external audit trail and triggering security alerts even if local logs are deleted.

### 3. Your application servers are running out of disk space because they retain 6 months of logs locally. What is the architectural fix?
**Answer:**
Keep application servers small and ship logs/metrics every few seconds to a centralized monitoring/logging store instead of keeping 3–12 months of logs on app disks.

### 4. A user reports an error when logging in across 11 devices, but the backend logs look normal. What is your first step?
**Answer:**
Always ask the user for **screenshots** of the error and trace the **session-wise** application logs to see the exact failure state.

### 5. You are asked to monitor a Kubernetes cluster without manually installing heavy forwarder agents on every node. What do you deploy?
**Answer:**
Deploy `kube-prometheus-stack` using **Helm** (`helm install prometheus prometheus-community/kube-prometheus-stack -n monitor`), which automatically sets up Prometheus, Grafana, `kube-state-metrics`, and `node-exporter`.

### 6. You try to install `kube-prometheus-stack` on GKE Autopilot and hit `Forbidden` / scheduler permission errors. What is the cause and fix?
**Answer:**
GKE Autopilot restricts certain node/scheduler permissions needed by the monitoring stack. Switch to a **GKE Standard** cluster, where `kube-prometheus-stack` installs cleanly.

### 7. You want to keep monitoring components isolated from application pods in Kubernetes. How do you do this?
**Answer:**
Create a dedicated namespace (`kubectl create namespace monitor`) and install the Helm chart into `-n monitor`.

### 8. You ran `helm install prometheus prometheus-community/kube-prometheus-stack -n monitor`, and it is taking 2–4 minutes. How do you verify it is healthy?
**Answer:**
Run `kubectl get pods -n monitor` and confirm all 5 monitoring pods are in the `Running` state.

### 9. You need to log into the Grafana UI right after Helm installation, but you don't know the `admin` password. How do you get it?
**Answer:**
Query the Grafana Kubernetes Secret in the `monitor` namespace (`kubectl get secret ... -n monitor`) and Base64-decode the `admin-password` field.

### 10. You are in Google Cloud Shell and want to quickly preview Grafana on port `3000` without creating a LoadBalancer. What do you do?
**Answer:**
Use `kubectl port-forward` to forward port `3000` from the Grafana pod/service to Cloud Shell, then open **Cloud Shell Web Preview** on port `3000`.

### 11. Your team needs permanent external browser access to Grafana on GKE. How do you expose it?
**Answer:**
Change/expose the Grafana Service type to **`LoadBalancer`** and run `kubectl get svc -n monitor` to get the External IP on port `3000`.

### 12. You open a default Grafana dashboard and several panels show `No data`. How do you troubleshoot?
**Answer:**
Open Grafana **Explore**, inspect the available Prometheus metrics, and adjust the PromQL query or math (for example, dividing by `1000`/`10000` or converting seconds to minutes) to match your cluster's metrics.

### 13. Your browser crashes or throws a chunk load error when opening heavy Grafana views on a small 1-node cluster. Why?
**Answer:**
Small clusters with limited resources can struggle under heavy Grafana queries/UI chunks. Use lighter dashboards or scale up cluster resources.

### 14. You need a detailed Kubernetes pod resource dashboard quickly without building PromQL queries from scratch. What do you do?
**Answer:**
Search Grafana's community dashboards, pick an ID (such as **`11594`** for Detailed Pod Resources), and use **New Dashboard → Import Dashboard** in Grafana, selecting Prometheus as the data source.

### 15. During an HPA performance test, your pod CPU is stuck at 48% and won't scale. What should you do?
**Answer:**
Generate more load (for example, opening multiple `busybox` load-generator tabs) or use smaller pod/cluster resource limits so CPU crosses the HPA threshold.

### 16. An interviewer asks why your company uses both Splunk and Prometheus/Grafana. How do you answer?
**Answer:**
Explain that Splunk is used as the **enterprise solution** (for production/compliance), while Prometheus + Grafana is **internally managed** for Dev/QA or Kubernetes workloads.

### 17. You are an L1/L2 engineer who monitors Grafana dashboards daily, while DevOps did the initial Terraform + Helm setup. How should you present this in an interview?
**Answer:**
Pitch the full end-to-end understanding—how the cluster is provisioned, how `kube-prometheus-stack` is installed via Helm in the `monitor` namespace, and how you monitor, troubleshoot, and alert using Grafana.

### 18. You need to customize Prometheus or Grafana settings before installing the Helm chart. Where do you make those changes?
**Answer:**
In the chart's **`values.yaml`** file before running `helm install` or `helm upgrade`.

### 19. A teammate asks why Helm v3 doesn't have a Tiller pod running in `kube-system`. How do you explain it?
**Answer:**
Tiller was the server-side daemon in Helm v2 and was removed in Helm v3+, where the Helm client communicates directly with the Kubernetes API.

### 20. You want to monitor network switches and routers where you cannot install an agent. Which monitoring type and protocol apply?
**Answer:**
**Network Monitoring**, which is typically **agentless** and uses **SNMP** or APIs.

---

## Quick Practical Revision Commands

```bash
# Check Helm version
helm version

# Create isolated monitoring namespace
kubectl create namespace monitor
kubectl get ns

# Add and update Prometheus community Helm repo
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

# Install kube-prometheus-stack
helm install prometheus prometheus-community/kube-prometheus-stack -n monitor

# Verify pods and services
kubectl get pods -n monitor
kubectl get svc -n monitor

# Retrieve Grafana secret (Base64-decoded password)
kubectl get secret -n monitor
```

---

## Interview Preparation Tip

Be ready to explain **why** continuous monitoring is required (scale, ephemeral auto-scaled nodes, security/audit trail, disk cost), the **three monitoring types**, and how **Helm + Prometheus (`kube-state-metrics`, `node-exporter`) + Grafana (PromQL, community dashboards)** work together in Kubernetes.
